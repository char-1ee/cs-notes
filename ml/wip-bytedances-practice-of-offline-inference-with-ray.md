# (WIP) ByteDance's practice of offline inference with Ray

## Use case

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption><p>ViT + Albert</p></figcaption></figure>

在这个案例中，我们同时对两个模型进行切分：一个 GPU 里面一部分放 Albert 的 Layers，另一部分是 Vit 的 Layers，其中 Embedding 层通常比较大，所以单独切到一个分组中。作业总共包含了3个 Stage，Stage 间传递 Image 和文本 Tokerns。因此这 3 个 Stage 所需的计算资源是不同的，即需要弹性分配算力的能力.

## Ray-based inference framework

TODO

## Ray deployment

每个 Ray Cluster 由 Head 节点和 Worker 节点组成，每个节点是一份计算资源，可以是物理机、Docker 等等，在 K8s 上即为一个 Pod。启动 Ray Cluster 时，使用 KubeRay 的 Operator 来管理整个生命周期，包括创建和销毁 Cluster 等.

<figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

用户可以通过内部的平台使用 Ray，通过提交 Job 或使用 Notebook 进行交互式编程。平台通过 Kuberay 提供的 YAML 和 Restful API 这两种方式进行操作。KubeRay 同时也支持自动扩展和水平扩展。Ray Cluster 在内部用于收集负载的 Metrics，并根据 Metrics 决定是否扩充更多的资源，如果需要则触发 Kuberay 拉起新的 Pod 或删除闲置的 Pod.

## Reference

{% embed url="https://zhuanlan.zhihu.com/p/636971612" %}
基于Ray的大规模离线推理
{% endembed %}

{% embed url="https://anyscale.com/blog/how-bytedance-scales-offline-inference-with-multi-modal-llms-to-200TB-data" %}
