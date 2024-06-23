# torch: DeviceMesh

`DeviceMesh`  is a high-level abstraction to manage `ProcessGroup`.  It is used to create inter-node and intra-node process groups without worrying about rank setups for different sub process groups.&#x20;

<figure><img src="../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

DeviceMesh is useful when working with multi-dimensional parallelism (i.e. 3-D parallel) where parallelism composability is required. For example, when your parallelism solutions require both communication across hosts and within each host. The image above shows that we can create a 2D mesh that connects the devices within each host, and connects each device with its counterpart on the other hosts in a homogenous setup.

Without DeviceMesh, users would need to manually set up NCCL communicators, cuda devices on each process before applying any parallelism, which could be quite complicated.

```python
from torch.distributed.device_mesh import init_device_mesh
mesh_2d = init_device_mesh("cuda", (2, 4), mesh_dim_names=("replicate", "shard"))

# Users access the underlaying process group via `get_group` API
replicate_group = mesh_2d.get_group(mesh_dim="replicate")
shard_group = mesh_2d.get_group(mesh_dim="shard")

# Run with torchrun
torchrun --nproc_per_node=8 2d_setup_with_device_mesh.py
```

### Shard group

A **shard group** refers to a subset of devices (GPUs) where each device holds a part of the overall data or model. In distributed training, sharding is often used to partition large datasets or models that do not fit into the memory of a single device. Each shard group processes only its segment of the data or model, reducing the memory requirements per device but necessitating coordination among groups to complete training tasks.

In above diagram showing a 2-D mesh of GPUs across two hosts, each with two GPUs, shard groups could be configured in various ways depending on the data distribution strategy. For instance, each row (Host 0 and Host 1) could represent a shard group where each GPU on the same host processes different portions of the data.

### Replicate group

A **replicate group**, on the other hand, contains a set of devices that maintain copies of the same data or model. In distributed training, replication is used to increase redundancy and improve fault tolerance. It also allows for parallel processing of different batches of data on the same model, enabling faster training through data parallelism.

A replicate group might be formed by the GPUs across hosts that share the same local GPU index (e.g., `cuda:0` across Host 0 and Host 1). This configuration ensures that each device in the replicate group has the same model state but may process different parts of the data during training.

In summary, in the DeviceMesh environment:

* **Shard groups** are concerned with how the dataset or model parameters are partitioned across multiple devices.
* **Replicate groups** focus on redundancy and consistency of the model parameters across devices, facilitating parallel processing of the data.
