---
description: Singularity container platform
---

# Singularity

```bash
# Get a singularity image and run (runscript)
$ singularity pull hello-world.sif shub://vsoch/hello-world
$ singularity run hello-world.sif

# Inspect run-script
$ singularity inspect -r hello-world.sif

# View singularity cache (where deleted images stored)
$ singularity cache list
$ singularity cache list -v

# Clean the cache 
$ singularity cache clean

# Run specific command within a container
$ singularity exec hello-world.sif /bin/echo Hello World!

# Open an interactive shell within a container
$ singularity shell hello-world.sif 
Singularity> 
```

Singularity binds some directories from the host system into the running container. By default, one directory is _home_, another is the current working directory where you started `singularity` command.&#x20;

```
Host system:                                                      Singularity container:
-------------                                                     ----------------------
/                                                                 /
├── bin                                                           ├── bin
├── etc                                                           ├── etc
│   ├── ...                                                       │   ├── ...
│   ├── group  ─> user's group added to group file in container ─>│   ├── group
│   └── passwd ──> user info added to passwd file in container ──>│   └── passwd
├── home                                                          ├── usr
│   └── jc1000 ───> user home directory made available ──> ─┐     ├── sbin
├── usr                 in container via bind mount         │     ├── home
├── sbin                                                    └────────>└── jc1000
└── ...                                                           └── ...

```

You can specify addtional host system directories to be available in the container by `-B`

```bash
# Bind additional host system directories to the container
$ singularity shell -B /work/z19/shared hello-world.sif
Singularity> ls /work/z19/shared

# Specify where a host directory is mounted in the container with :
$ singularity shell -B /work/z19/shared:/shared-data hello-world.sif
Singularity> ls /shared-data
```

### References

{% embed url="https://epcced.github.io/2021-07-29_Singularity_Online/" %}
