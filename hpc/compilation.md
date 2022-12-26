# Compilation

### Compliers

* GCC: C -> gcc, C++ -> g++, Fortran -> gfortran&#x20;
* Intel: C -> icc, C++ -> icpc, Fortran -> ifort

### GCC

#### Linkages

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

#### Files

* `-o`: set the name of the output file
* `-C`: Compile or assemble the source files, but do not link, output (.o) file
* `-S`: Stop after the stage of compilation proper; do not assemble. The output is in the form of an assembler code file (.s)
* `-E`: Stop after preprocessing phase, output preprocessed (.i) file.
* `-save-temps`: Do not delete intermediate files (.i .s .o)

#### Optimizations

* `-flto` is link time optimization
* `-funroll-loops` is to unroll loops in the code
* `-march=native` for host CPU’s architecture
* `-march=<arch>` for the specified architecture

#### Add macros (for preprocessing)

Use `-D<macro>` (optionally `-D<macro>=<definition>` for values) to add macros

```bash
$ gcc -DUSE_MPI main.c
```

* Defines the “USE\_MPI” macro (equals to “`#define USE_MPI`”)
* Used by the preprocessor
* Useful for things like selective compilation

#### Enable OpenMP

gcc: `-fopenmp`, Intel: `-qopenmp`

```bash
$ gcc -fopenmp omp_test.c -o omp_test
$ export OMP_NUM_THREADS=2
```
