---
description: v0.5.0.post1
---

# (WIP) vLLM

## Distributed with Ray

* 提供ray或者mp两种方式来调度, tp/pp 默认ray -> distributed\_executor\_backend
* ParallelConfig当中判断distributed\_executor\_backend 逻辑

**Ray 初始化**

1. LLMEngine init from\_engine\_args 过程中，调用initialize\_ray\_cluster()做3件事情：
   1. &#x20; 1\) ray.init() connects to a ray cluster;  2) create placement group， 然后把placement group 信息存在parallel\_config里.  3) vLLM Ray init的时候检查whether placement group is set. If not, we create -> ray.util.placement\_group(); else, 把gpu bundle 信息加入现有placement group.
2. Init ray cluster 之后创建llm engine executor\_class by RayGPUExecutor class. (executor\_class -> model\_executor)
3. RayGPUExecutor(DistributedGPUExecutor): (USE\_RAY\_COMPILED\_DAG)
   1. 创建GPU workers: \_init\_workers\_ray(placement\_group). \_init\_workers\_ray()创建worker and driver (这里的driver并不给driver上的worker分配资源).
      1. Workers 和driver -> RayWorkerWrapper (vllm.worker上包了一层), 用于lazy init
      2. Create the workers: 分配ip, bundle, scheduling strategy -> worker = ray.remote()(RayWorkerWrapper).remote() -> create driver
      3. Init worker: \_run\_workers(method, args) 在每个worker上执行一个相同的method -> 对于workers, 执行的methods有 ...init\_worker (WorkerWrapperBase) ,init\_device, load\_model (Worker内的method)
         1. init\_worker (WorkerWrapperBase): lazily init worker
         2. init\_device (Worker): torch.cuda.set\_device() -> init\_worker\_distributed\_environment(): 设置分布式环境 (这一部分通过与vllm.distributed交互来set up分布式通信):
            1. set\_custom\_all\_reduce()
            2. init\_distributed\_environment(): torch.dist.init\_process\_group()
            3. ensure\_model\_parallel\_initialized: 初始化 model parallel groups, 主要是tp, pp的逻辑了
         3. load\_models (Worker) -> model\_runner.load\_model() 这一块就和ModelRunner交互来选择和加载模型

**Ray 执行**

* engine.step() -> self.model\_executor.execute\_model(), 由于execute\_model 是ExecutorBase的接口，RayGPUExecutor <- DistributedGPUExecutor <- GPUExecutor <- ExecutorBase, 所以会调用DistExecutor的execute\_model()， which 做了两件事 (在driver上和在worker上):
  * start\_worker\_execution\_loop (Worker): 循环执行worker里的\_execute\_model\_non\_driver(), which 在并行的worker上执行model, 然后扔给model\_runner执行execute\_model
  * \_driver\_execute\_model(): 这里是abstractmethod所以control转移到RayExec, RayExec这里调用driver worker的execute\_model方法, which is Worker.execute\_model. Worker 的execute\_model() 预处理(broadcast, manage memory)后扔给model\_runner开始推理

**Why Ray?**

1. SP/PP这些并行策略本质上还是通过 torch.distributed (NCCL)通信
2. vLLM使用Ray 来管理多机多卡环境, multiprocessing, torchrun可以做相同的事情，所以不使用Ray不会影响到SP/PP/TP
3. 所以为什么要用Ray呢？首先vLLM 有成熟的实现，可以加速我们的开发；其次接入Ray的生态，方便我们使用Ray提供的一系列orch功能，以及和K8S集成; 当然更可能是因为Ray是同一个lab的工作，属于是夹带私活了.

[https://github.com/vllm-project/vllm/issues/3587](https://github.com/vllm-project/vllm/issues/3587)\
[https://github.com/vllm-project/vllm/pull/4539](https://github.com/vllm-project/vllm/pull/4539)
