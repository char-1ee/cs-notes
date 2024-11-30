---
description: https://www.zhihu.com/question/460421443/answer/3603399915
---

# Distributed AI task scheduling

In a heterogeneous computing scenario, different nodes within a cluster have different status and compute capabilities (e.g. GPU). When a pod is created, it relies on a **Scheduler** to arrange it to certain node.&#x20;

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption><p>K8S, Pod, Node</p></figcaption></figure>

Conventionally, the `kube-scheduler` meets all pod-to-node scheduling needs, by k8s native support including node affinity, taint and tolerance, nodeSelector etc. It adopts Predicate and Priority polices:

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption><p>How kube-scheduler arrange pods</p></figcaption></figure>

Predicate: remove the nodes without enough resources and the nodes that violates users' polices; Priority: score remaining nodes and finally arrange the pod to the node with highest score.

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption><p>Example</p></figcaption></figure>

However, in production environment, it is still users's responsibility to consider a proper scheduling strategy that maximizes resource utilites, maximizes Pod performance, avoid idle of machine and waste of resources.

### Challenge: AI task

**Compute resources** (GPU, RDMA NIC):

1. CPU, RAM can be scheduled at a low granularity, GPU and RDMA NIC cannot;
2. CPU, RAM allows Burstable scheduling, i.e. allows resource limits higher than actual use. GPU and RDMA keeps strict consistent (limit=actual use);

**Resource fragmentation**: A task Pod needs to use two GPU cards, and there are exactly two GPU cards idle in the cluster, but These two idle GPU cards are distributed on two different nodes, causing this task to be unable to be run.&#x20;

**Runtime difference**: A web service maintains multiple replicas, it can successfully run with at 1 pod. AI task has distributed workload, all pods need to collaborate.

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption><p>Runtime difference</p></figcaption></figure>

### Scheduling algorithms

#### Gang scheduling (All or Nothing)

* Ensure a task either successfully schedule all pods or failed all pods.
* Fit for resource intensive and synchronized tasks, e.g. AI, ML, HPC.
* It simulates a task run to check if the cluster can bear the task. If yes, apply the simulation. If no, abandon the task.&#x20;

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

#### Multi-tenant scheduling

Iterate all tasks in queue, if the current tenant's resource quota allows for his task's resource needs, schedule it. Else skip the task.

#### Anti-fragmentation scheduling

The scheduler applies a scheduling policy that avoid GPU fragmentation as much as possible. Example 1, when scheduling a Pod, it choose a node that already being partially occupied, such that other vacant nodes in cluster are ready for big tasks. Example 2, when scoring nodes, consider taking in the number of remaining GPU cards in that node as a factor, to avoid odd number of GPU (1, 3, 5, 7).

Note that for some large-scale AI training tasks, a whole node is regarded as a unit of compute resource, e.g. a Pod = a Node.
