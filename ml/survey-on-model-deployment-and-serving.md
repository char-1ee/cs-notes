# Survey on model deployment and serving

## Serving frameworks

### Paddle Serving

{% embed url="https://github.com/PaddlePaddle/Serving" %}
Paddle serving
{% endembed %}

* Serving design [https://github.com/PaddlePaddle/Serving/blob/v0.9.0/doc/Serving\_Design\_CN.md](https://github.com/PaddlePaddle/Serving/blob/v0.9.0/doc/Serving\_Design\_CN.md)
* Pipeline design [https://github.com/PaddlePaddle/Serving/blob/v0.9.0/doc/Python\_Pipeline/Pipeline\_Design\_CN.md](https://github.com/PaddlePaddle/Serving/blob/v0.9.0/doc/Python\_Pipeline/Pipeline\_Design\_CN.md)

### OpenPPL (C++)

OpenPPL-LLM 推理系统包含 4 部分：

1. ppl.llm.serving 是服务框架，是推理服务系统的入口: [https://github.com/openppl-public/ppl.llm.serving](https://github.com/openppl-public/ppl.llm.serving)
2. ppl.pmx 包含 pmx算子标准的文档以及PythonAPI，以及官方模型的ModelZoo;
3. ppl.nn.llm 是推理引擎，负责核心网络部分推理加速: [https://github.com/openppl-public/ppl.nn.llm](https://github.com/openppl-public/ppl.nn.llm)
4. ppl.llm.kernel.cuda 包含针对大模型开发的高性能 kernel

### TorchServe

{% embed url="https://pytorch.org/serve/" %}

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption><p>Arch</p></figcaption></figure>

### Torchrun

准备好torch distributed init\_process\_group的脚本，然后分别在每个节点上跑 (e.g. 4 process each node)

```bash
torchrun --nproc-per-node 4 test.py
```

然后torchrun的时候指定主节点地址 `--master-port=<>`, 即可跨节点通信 (一般框架帮你自动化). 本质上来说intra-node是nccl通信，然后inter-node是tcp通信 `torch.distributed.init_process_group`的`init_method`可以指定`tcp`, `file`, `env`:

TCP

> `TCP` 方式初始化，需要指定进程 `0` 的 `ip` 和 `port`。这种方式需要手动为每个进程指定进程号. 不同进程内，均使用主进程的 `ip` 地址和 `port`，确保每个进程能够通过一个 `master` 进行协作。该 `ip` 一般为主进程所在的主机的 `ip`，端口号应该未被其他应用占用。实际使用时，在每个进程内运行代码，并需要为每一个进程手动指定一个 `rank`，进程可以分布与相同或不同主机上.

File

> 以 `file://` 为前缀，表示文件系统各式初始化。`/mnt/nfs/sharedfile` 表示共享的文件，各个进程在共享文件系统中通过该文件进行同步或异步。因此，所有进程必须对该文件具有读写权限。每一个进程将会打开这个文件，写入自己的信息，并等待直到其他所有进程完成该操作。在此之后，所有的请求信息将会被所有的进程可访问，为了避免 `race conditions`，文件系统必须支持通过 `fcntl` 锁定（大多数的 `local` 系统和 `NFS` 均支持该特性）

Env&#x20;

> 默认情况下使用的都是环境变量来进行分布式通信，也就是指定 `init_method="env://"`。通过在所有机器上设置如下四个环境变量，所有的进程将会适当的连接到 `master`，获取其他进程的信息，并最终与它们握手(信号)。
>
> * `MASTER_PORT`: 必须指定，表示 `rank0`上机器的一个空闲端口（必须设置）
> * `MASTER_ADDR`: 必须指定，除了 `rank0` 主机，表示主进程 `rank0` 机器的地址（必须设置）
> * `WORLD_SIZE`: 可选，总进程数，可以这里指定，在 `init` 函数中也可以指定
> * `RANK`: 可选，当前进程的 `rank`，也可以在 `init` 函数中指定

### Ray Serve

* serve接口主要提供集群管理, API服务的接口，比如autoscaling, ingress, endpoints etc.
* Example: Ray serve with vLLM [https://docs.ray.io/en/latest/serve/tutorials/vllm-example.html](https://docs.ray.io/en/latest/serve/tutorials/vllm-example.html)

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption><p>Example</p></figcaption></figure>

### Ray Cluster

* While Ray works out of the box on single machines with just a call to `ray.init`, to run Ray applications on multiple nodes you must first _deploy a Ray cluster_.
* Ray Cluster serves as a intermediate layer, can be deployed on [public cloud](https://cloud.google.com/blog/products/ai-machine-learning/build-a-ml-platform-with-kubeflow-and-ray-on-gke), [bare metal machines](https://docs.ray.io/en/master/cluster/vms/user-guides/launching-clusters/on-premises.html#on-prem), [K8S](https://docs.ray.io/en/master/cluster/vms/user-guides/launching-clusters/on-premises.html#on-prem) (Ray node running as pod)

手动设置cluster

1. 在worker, head上打开所有端口（否则可能出现bug）

```bash
sudo iptables -P INPUT ACCEPT 
sudo iptables -P FORWARD ACCEPT
sudo iptables -P OUTPUT ACCEPT
sudo iptables -F
sudo iptables-save
```

2. Start head node

```bash
ray start --head --port=6379
```

3. Then start worker node

```bash
ray start --address='<head-address:port>'
```

4. Finally, specify the head node socket in code

```bash
ray.init(address="<head-address:port>")
```

### Ray runtime

Ray runtime is started

* Implicitly via `ray.init()` ([Starting Ray on a single machine](https://docs.ray.io/en/master/ray-core/starting-ray.html#start-ray-init)) -> Single node
* Explicitly via CLI ([Starting Ray via the CLI (ray start)](https://docs.ray.io/en/master/ray-core/starting-ray.html#start-ray-cli))

```bash
# Start a 1 node ray runtime on 1 machine. This machine turns to head node
ray start --head --port=6379

# Start worker nodes
ray start --address=<head-node-address:port>
```

You can connect to this Ray instance by starting a driver process on the same node as where you ran `ray start`. `ray.init()` will now automatically connect to the latest Ray instance.

* Explicitly via the cluster launcher ([Launching a Ray cluster (ray up)](https://docs.ray.io/en/master/ray-core/starting-ray.html#start-ray-up))
