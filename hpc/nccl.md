---
description: Nvidia Collective multi-GPU Communication Library
---

# NCCL

### Communication primitive

1. Point-to point communication (1 sender + 1 receiver)
2. Collective communication (multi-sender + multi-receiver): broadcast, gather, all-gather, scatter, reduce, all-reduce, reduce-scatter, all-to-all.

Receive data from multi-sender and combine to one node

<figure><img src="../.gitbook/assets/image (3).png" alt="" width="563"><figcaption><p>Reduce</p></figcaption></figure>

Receive data from multi-sender and combine to every node

<figure><img src="../.gitbook/assets/image (1) (1) (1).png" alt="" width="563"><figcaption><p>All-reduce</p></figcaption></figure>

Collective communication assumes the topology of nodes is a fat tree, which has highest communication efficiency. But real topology could be more complex, then ring-based collective communication is applied.

### Ring-based collectives

Ring-based collectives form a directed cyclic ring with all nodes and transmit data sequencially around the ring.&#x20;

<figure><img src="../.gitbook/assets/v2-12af80e172e09cc92e4e6dcde6841311_720w.png" alt="" width="313"><figcaption><p>GPU0 -> GPU1 -> GPU2 -> GPU3</p></figcaption></figure>

Assume data is N, bandwidth is B, then total transmission time is (K - 1) N / B.

<figure><img src="../.gitbook/assets/v2-fed2f439627bc1c16bec63cc7ec84cdf_720w (1).png" alt="" width="314"><figcaption><p>Transmit N/S data each time</p></figcaption></figure>

Assume data is divided into N/S, then total time is S\*(N/S/B) + (k-2)\*(N/S/B) = N(S+K-2)/(SB). When S >> K, the time is N/B, which means communication time wont increase with number of nodes.&#x20;

How to form a ring:

<figure><img src="../.gitbook/assets/v2-dac47e37dedf4ce07c92861c138b91e7_720w.png" alt="" width="293"><figcaption><p>Sngle node, 4 GPU with PCIe</p></figcaption></figure>

<figure><img src="../.gitbook/assets/v2-1400c6742580fabed45eb4d02553df83_720w.png" alt="" width="540"><figcaption><p>Single node, 8 GPU with 2 PCIe switch</p></figcaption></figure>

### NCCL implementation

* 3 primitives: Copy, Reduce, ReduceAndCopy
* Start from NCCL 2.0 supporting multi-node, multi-cards

<figure><img src="../.gitbook/assets/v2-5614a5e2da87f34b0b76eabe40339f35_720w.png" alt=""><figcaption><p>Form communication ring</p></figcaption></figure>

* Ring-based algorithm scales latency with number of GPUs. Thus, new algorithms like 2D ring algorithm introduced in NCCL 2.4 to replace flat ring algorithm.

### Practices

* \`torch.distributed()\` supports 3 native backend: NCCL, Gloo, MPI.&#x20;
* Suggested to use nightly build of NCCL (>=2.4) from source for ML model training.

### Reference

{% embed url="https://www.zhihu.com/question/63219175/answer/206697974" %}

{% embed url="https://developer.nvidia.com/blog/massively-scale-deep-learning-training-nccl-2-4/" %}
