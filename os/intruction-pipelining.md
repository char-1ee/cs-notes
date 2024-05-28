# Intruction pipelining

Classic RISC pipelines:

1. Intruction fetch (IR)
2. Instruction decode and register fetch (ID)
3. Execute (EX)
4. Memory access (MEM)
5. Register write back (WB)

By clock signal, these steps form a pipeline:

<figure><img src="../.gitbook/assets/image (1) (1).png" alt="" width="476"><figcaption></figcaption></figure>

### Instruction Level Parallelism

Each phrase uses different hardware resources, such that the execute periods of them can be overlapped.&#x20;



<figure><img src="../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption><p>ILP</p></figcaption></figure>

### Superscalar

Superscalar, implements multiple instruction pipeline in CPU to realize real instruction parallelism. In each clock cycel, CPU reads 2 instructions simultaneously, decodes them, executes and writes back at the same time.&#x20;

<figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

### Pipeline hazard

There is a chance that these sets of instructions will become dependent on one another, reducing the pipeline’s pace.

* Data Hazard
* Control Hazard
* Structure Hazard

#### Data hazard

2nd instruction needs the results of 1st instruction such that 2nd instruction stops until 1st instruction stops.

```c
x = 1;
y = x;
```

#### Control hazard

CPU cannot predict next instruction when branching

```c
if (a > 100) {
    x = 1;
} else {
    y = 2;
}
```

So either CPU stops until know next instruction to go, or using **branch prediction** and **speculation execution**.

#### Structure hazard

Multiple instructions race for one same hardware resource.
