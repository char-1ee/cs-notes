---
description: Memory bound, compute bound, latency bound
---

# GPU Bound

GPU performance bottleneck:

* Compute Bound
* Memory Bound
  * Memory Bandwidth Bound
  * Memory Latency Bound

### Memory Bandwidth Bound

In broad sense, refers to the cost of moving data from one place to another place, i.e. node to node, CPU to GPU, GPU global memory (DRAM) to shared memory (SRAM).

In narrow sense, refers to the cost transfering data between GPU DRAM and SRAM -> Operator Fusion.

> GPU 上的内存分为全局内存（DRAM）和共享内存（SRAM）两种，前者空间更大但是访问速度更慢，后者空间更小但是访问速度更快。一个通常的计算流程是，首先将需要计算的数据从 DRAM 加载到 SRAM 中，随后基于 SRAM 上的数据进行计算，最后将 SRAM 的数据写回到 DRAM 中。假如在这个过程中，因为 DRAM 和 SRAM 之间的带宽有限，我们从 DRAM 中加载数据和将数据写回到 DRAM 中的时间占据了整个过程的绝大部分时间，我们的程序就属于 memory bandwidth bound.

### Memory Latency Bound

> 对于一个 bandwidth bound 的程序，我们可以通过在更大的集群上运行使其变得更快；但对于一个 latency bound 的程序则不然，除非新的集群有着不同架构和更低的延迟。
>
> 假如我们想要向一个 1GB 的数组中写入 0：当一个处理器的带宽是 40GB/s 时，这个过程需要花费 25ms；而当我们使用两个这样的处理器同时写入时，这个过程将花费 12.5ms，因为我们的带宽变成了 80GB/s。
>
> 再假如我们想要访问 32MB 的指针链（1,000,000 个随机顺序的指针）：当使用桌面级处理器时，这个过程通常需要花费 100,000,000 个周期，而当使用服务器级处理器时，这个过程则只需要花费 50,000,000 个周期。二者的区别在于桌面级处理器的片上缓存空间不足以存放下全部的指针链，而服务器级处理器可以，访问片上缓存所花费的周期小于访问主存。
>
> 在第一个例子中，我们取得加速不是因为使用了不同的技术，而单纯因为使用了更多的带宽资源；而第二个例子中，我们取得加速只能通过使用不同的硬件。事实上这两个例子就分别对应着 HPC Challenge benchmark 中的 **PTRANS（Parallel Transpose）**和 **Random Access**，一般而言优化前者比后者更容易。

Latency bound -> bandwidth bound

> 很多性能优化本质上是将 latency bound 转化为 bandwidth bound，比如常见的访存连续化，就是将多次不连续的访存合并为一次连续的访存，从而只花费一次访存的 latency 而增加对 bandwidth 的利用.

### First Principle

If you want to speed up your deep learning system, the most important thing is to understand what the bottleneck in your model is. That bottleneck determines what the appropriate way of speeding up your system is.

| Performance Regime | Plausible Solutions                                          |
| ------------------ | ------------------------------------------------------------ |
| Overhead-Bound     | Tracing, Operator Fusion, don't use Python, a _real_ JIT :^) |
| Bandwidth-Bound    | Operator Fusion                                              |
| Compute-Bound      | Use Tensor Cores, give Nvidia more money                     |

> Of course, arguably, users needing to think about this stuff at all reflects a failure on the part of the framework.

## Reference

{% embed url="https://zhuanlan.zhihu.com/p/673957960" %}

{% embed url="https://horace.io/brrr_intro.html" %}
