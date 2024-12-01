# SplitWise

### Insights

1. 不同的推理任务中，Prefill和Decode的分布不同
2. 在P/D混合部署中，大部分时间消耗在Decode阶段，未充分利用到GPU的算力
3. 对大部分的请求，E2E时间消耗在Decode阶段
4. Prefill阶段应该限制Batch size从而避免影响性能，相反，Decode阶段应该增大Batch size来获得更高的吞吐
5. Prefill阶段算力是瓶颈，Decode阶段内存是瓶颈
6. Prefill阶段能充分使用算力，Decode阶段不能
7. Decode阶段可以使用性能较弱、成本较低的设备

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### KV cache transfer

这种分离架构的方案引入的最大开销就是把KV Cache从Prefill阶段迁移到Decode阶段的开销。其为了减小这方面的开销，也做了一些优化：

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

(a) 如果使用的是未经优化的线性KV Cache传输，那么所经历的时间就包括Prompt时间，等待KV Cache传输的时间和Token的时间。

(b) 是SplitWise优化后的KV Cache传输，他们提出了Layer-wise的方法，以层为单位，在Prompt阶段，算完一层的KV Cache就开始传

> In Splitwise, we optimize the KV-cache transfer by overlapping it with the computation in the prompt phase. As each layer in the LLM gets calculated in the prompt machine, the KV cache corresponding to that layer is also generated. At the end of each layer, we trigger an asynchronous transfer of the KVcache for that layer while the prompt computation continues to the next layer. Figure 11b shows this asynchronous transfer which reduces the transfer overheads. Layer-wise transfer also enables other optimizations, such as earlier start of the token phase in the token machines, as well as earlier release of KV-cache memory on the prompt machines.&#x20;
>
> Layer-wise KV-cache transfer happens in parallel with the prompt computation for the next layer. This requires finegrained synchronization per layer for correctness. Thus, it is possible to incur performance interference and increase the TTFT, especially for smaller prompts. However, for small prompts the total KV-cache size is small and does not need the layer-wise transfer to hide the latency. Since the number of tokens in a batch is already known at the start of computation, Splitwise picks the best technique for KV-cache transfer. It uses serialized KV-cache transfer for smaller prompts and layer-wise transfer and for larger prompts. We show that the overall transfer and interference overheads are relatively small in Section VI-A.

{% embed url="https://zhuanlan.zhihu.com/p/701772045" %}
