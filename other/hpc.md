---
description: About High Performance Computing
---

# HPC

## Performance measurements

### **Walltime**

Walltime is the length of time, usually measured in seconds, that a program takes to run (i.e., to execute its assigned tasks). It is not directly related to the resources used, it’s the time it takes according to an independent clock on the wall.

### **Flops**

Flops (or sometimes flop/s) stands for floating point operations per second and they are typically used to measure the (theoretical) performance of a computer’s processor.

Theoretical peak flops is given by:

```bash
Number of cores * Average frequency * Operations per cycle
```

What a software program can _achieve_ in terms of flops is usually a surprisingly small percentage of this value (e.g., 10% efficiency is not a bad number!).

### **CPU hours**

CPU hours is the amount of CPU time spent processing. For example, if I execute for a walltime of 1 hour on 10 CPUs, then I will have used up 10 CPU hours.
