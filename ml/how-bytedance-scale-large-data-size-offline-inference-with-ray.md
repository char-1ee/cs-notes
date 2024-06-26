# How ByteDance scale large data size offline inference with Ray

Use case

<figure><img src="../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption><p>ViT + Albert</p></figcaption></figure>

在这个案例中，我们同时对两个模型进行切分：一个 GPU 里面一部分放 Albert 的 Layers，另一部分是 Vit 的 Layers，其中 Embedding 层通常比较大，所以单独切到一个分组中。作业总共包含了3个 Stage，Stage 间传递 Image 和文本 Tokerns。因此这 3 个 Stage 所需的计算资源是不同的，即需要弹性分配算力的能力.

## Ray-based inference framework

Ray Actor + Ray ObjectStore

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption><p>V1</p></figcaption></figure>

> 而使用 Ray 就只需要启动两个 Actor，对应 Spark 的两个 Executor。但是这两个 Actor 可以分别加载两个模型分组的参数，两个 Actor 间的执行过程可以 Pipeline 起来，数据样本依次经过两个 Actor。此外也可以非常方便地再增加一个 CPU 上的 Actor 专门做数据的读取或存储。框架通过使用 Ray ObjectStore 存储中间结果数据，纯内存存储避免了序列化的开销，并且可以显著提高执行效率，由此可见在此类场景下，使用 Ray 相比于 Spark 可以显著地提升效率。

> 仍然存在一些问题：
>
> * 每个 Window 都要创建和销毁 Actor Pool，而每次拉起 Actor 加载模型的开销太大；
> * 在每个 Actor 进行推理时，数据的 IO 和推理过程并没有并行起来，导致 GPU 利用率不高；
> * Actor Pool 缺乏弹性，在每个 Stage 算力需求不一样的情况下会浪费资源，需要不断调整参数以解决这个问题；
> * 使用 API 调试参数困难；
> * 缺乏容错和推测执行能力。

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>V2</p></figcaption></figure>

> 为了解决以上问题，我们开发了第二版推理框架。深入到 Ray Datasets Pipeline 的内部实现中添加了 Streaming 执行语义。各个 Stage 通过 Queue 前后连接起来，Queue 中传递的是 Ray Object Reference 而不是实际数据，实际数据在 Actor 侧。相当于我们写程序时函数之间传递指针数组而不是实际数据。
>
> 第二版推理框架和第一版不同，每一个 Stage 背后是一个稳定的 Actor Pool，从一开始被创建之后就不会释放。
>
> 在运行的过程中，该 Stage 就从它的 Input Queue 中读取 Object Reference，读到数据后在自己的 Actor Pool 中选择一个 Actor 来处理数据。因为 Actor Pool 是自定义的，可以实现弹性能力，使负载重的 Stage 的 Actor Pool 会主动尝试申请更多的资源来增加自己的并行度，而负载轻的 Stage 的 Actor Pool 会逐渐空闲下来，并最终释放掉一些 Actor，从而出让资源给需要资源更多的 Stage。
>
> 当然，这个也需要配合一定的调度策略，也就是 Stage 在分发数据的时候如何选择一个 Actor。我们现在使用的是 Most Recently Used 的策略，忙的 Actor 就让它更忙，这样空闲的 Actor 就可以容易空闲下来释放掉。在 Actor 侧，利用 Actor 内多线程实现 IO 和推理计算并行，提高了 GPU 的利用率。

## Ray deployment

每个 Ray Cluster 由 Head 节点和 Worker 节点组成，每个节点是一份计算资源，可以是物理机、Docker 等等，在 K8s 上即为一个 Pod。启动 Ray Cluster 时，使用 KubeRay 的 Operator 来管理整个生命周期，包括创建和销毁 Cluster 等.

<figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

用户可以通过内部的平台使用 Ray，通过提交 Job 或使用 Notebook 进行交互式编程。平台通过 Kuberay 提供的 YAML 和 Restful API 这两种方式进行操作。KubeRay 同时也支持自动扩展和水平扩展。Ray Cluster 在内部用于收集负载的 Metrics，并根据 Metrics 决定是否扩充更多的资源，如果需要则触发 Kuberay 拉起新的 Pod 或删除闲置的 Pod.

## Reference

{% embed url="https://zhuanlan.zhihu.com/p/636971612" %}
基于Ray的大规模离线推理
{% endembed %}

{% embed url="https://anyscale.com/blog/how-bytedance-scales-offline-inference-with-multi-modal-llms-to-200TB-data" %}
