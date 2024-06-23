# GPU networking

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption><p>Summary</p></figcaption></figure>

Single node multi-card networking:

* GPUDirect (P2P, Storage)
* NVLink (NVSwitch)

Multi-node multi-card networking:

* GPUDirect (RDMA)
* RDMA (IB, RoCE, iWARP)

### GPUDirect

* GPUDirect P2P (Direct access + direct transfer)

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption><p>P2P</p></figcaption></figure>

* GPUDirect RDMA: data transferring between GPU and RDMA&#x20;

<figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption><p>GRDMA</p></figcaption></figure>

* GPUDirect Storage

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

普通存储IO路径

1. 访问NIC/NVMe，获取存储数据，传输至PCIe Switch
2. PCIe Switch传输支持CPU
3. CPU传输至System Memory
4. System Memory传输CPU
5. CPU传输至PCIe Switch
6. PCIe Switch 传输至GPU

使能GDS功能后，存储IO路径

* NIC/NVMe数据可以经由PCIe Switch直接传输至GPU设备

CUDA API接口

* cuFile类接口，均为GDS功能相关实现

### NVLink

NVLink allows communication between 8 card in a node once a time. If exceed 8 cards, then there needs NVSwitch to implement many-to-many communication.

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption><p>NVSwitch</p></figcaption></figure>

