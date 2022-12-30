---
description: Message Passing Interface
---

# MPI

Run program on MPI runtime system -> implemented with MPI standard

Four important concepts:

* COMM\_WORLD: the default MPI Communicator, providing a channel for all the processes involved in this `mpiexec` to exchange information with one another.
* Scatter: A collective operation in which an array of data on one MPI rank is divided up, with separate portions being sent out to the partner ranks. Each partner rank receives data from the matching index of the host array.
* Gather: The inverse of scatter. One rank populates a local array, with the array element at each index assigned the value provided by the corresponding partner rank – including the host’s own value.
* Conditional Output: since every rank is running the _same code_, the partitioning, the final calculations, and the `print` statement are wrapped in a conditional so that only one rank performs these operations.

In practice, MPI speedup factor is influenced by:

* CPU design
* Communication network between nodes
* The MPI library implementations
* Details of MPI program
