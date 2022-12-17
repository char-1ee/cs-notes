---
description: About High Performance Computing
---

# Get started

## Performance measurements

* **Walltime:** Walltime is the length of time, usually measured in seconds, that a program takes to run (i.e., to execute its assigned tasks). It is not directly related to the resources used, it’s the time it takes according to an independent clock on the wall.
* **Flops:** Flops (or sometimes flop/s) stands for floating point operations per second and they are typically used to measure the (theoretical) performance of a computer’s processor. What a software program can _achieve_ in terms of flops is usually a surprisingly small percentage of this value (e.g., 10% efficiency is not a bad number!). Theoretical peak flops is given by:

```bash
Number of cores * Average frequency * Operations per cycle
```

* **CPU hours:** CPU hours is the amount of CPU time spent processing. For example, if I execute for a walltime of 1 hour on 10 CPUs, then I will have used up 10 CPU hours.

## Parallelism

### Shared memory vs Distributed memory

<figure><img src="../.gitbook/assets/image (1) (1) (2).png" alt=""><figcaption></figcaption></figure>

### Parallelizing strategies

*   For shared memory structure, use _OpenMP_ on multi-core/many-core CPU which provides thread level parallelism, use CUDA on GPU (aka. accelerator).&#x20;

    <figure><img src="../.gitbook/assets/fork_join (1).png" alt=""><figcaption><p>OpenMP Fork-join model</p></figcaption></figure>
* For distributed memory structure, use _MPI_ to communicate between workers (a message passing protocol), which is capable of runnning parallel program on a set of diverse architectures.
* Generally, OpenMP is fit for thread-level parallelism while MPI is fit for process-level parallelism. OpenMP works on a single node, MPI can work on multiple nodes.

The two main parallelization strategies are **data parallelism** and **task parallelism**.&#x20;

| Data Parallelisms                                                                                          | Task Parallelisms                                                                                                                        |
| ---------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| <p>1. Same task are performed on different subsets of same data.<br></p>                                   | <p>1. Different task are performed on the same or different data.<br></p>                                                                |
| <p>2. Synchronous computation is performed.<br></p>                                                        | <p>2. Asynchronous computation is performed.<br></p>                                                                                     |
| <p>3. As there is only one execution thread operating on all sets of data, so the speedup is more.<br></p> | <p>3. As each processor will execute a different thread or process on the same or different set of data, so speedup is less.<br></p>     |
| <p>4. Amount of parallelization is proportional to the input size.<br></p>                                 | <p>4. Amount of parallelization is proportional to the number of independent tasks is performed.<br></p>                                 |
| <p>5. It is designed for optimum load balance on multiprocessor system.<br></p>                            | 5. Here, load balancing depends upon on the e availability of the hardware and scheduling algorithms like static and dynamic scheduling. |

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

## Benchmarking and Scaling

Benchmarking is a way of assessing the performance of a program (or set of programs), and benchmark tests are designed to mimic a particular type of workload on a component or system.

[Scaling ](https://hpc-wiki.info/hpc/Scaling)concerns the effective use of computational resources. Two types are typically discussed: **strong scaling** (where we increase the compute resources but keep the problem size the same) and **weak scaling** (where we increase the problem size in proportion to our increase of compute resources).&#x20;

To fold a protein in 1 hour:

* Strong scaling (run problem faster with same problem size): fold same protein in 1 minute
* Weak scaling (run a larger problem or more problem): fold a bigger protein in 1 hour OR fold many small protein in 1 hour

## HPC filesystem

* Home - a network filesystem, available throughout the whole HPC system and backed up periodically. Slow to access. Data is transmitted over the network.&#x20;
* **Scratch** - faster than the Home directory, not usually backed up. Used for short term storage for a single job or set of computational jobs
