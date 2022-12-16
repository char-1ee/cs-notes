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

Singularity can pull docker images from docker hub.  Singularity pulls the slices or _layers_ that make up the Docker image and converts them into a single-file Singularity SIF image.

```bash
$ singularity pull python-3.9.6.sif docker://python:3.9.6-slim-buster
$ singularity run python-3.9.6.sif
```

`--fakeroot` allows sudo command for non-root users to package (tar, zip) files and build images.

### Build Singularity images (.sif) with Singularity Definition File (.def)

```bash
# Where to bootstrap image from
Bootstrap: docker
From: ubuntu:20.04

# Shell commands run in images context
%post
    apt-get -y update && \
    apt-get install -y python3

# Define the runscript
%runscript
    python3 -c 'print("Hello World! Hello from our custom Singularity image!")'
```

```bash
$ singularity build /home/test_image.sif /home/test_image.def
```

For more definition file writings:\
[https://docs.sylabs.io/guides/3.5/user-guide/definition\_files.html#sections](https://docs.sylabs.io/guides/3.5/user-guide/definition\_files.html#sections)

### Use `scp` __ (secure copy) to copy files between systems

You may find that the created Singularity image file on your host filesystem is owned by the `root` user and not your user. In this case, you won’t be able to change the ownership/permissions of the file directly if you don’t have root access.

However, the image file will be readable by you and you should be able to take a copy of the file under a new name which you will then own. You will then be able to modify the permissions of this copy of the image and delete the original root-owned file since the default permissions should allow this.

It is recommended that you move the created `.sif` file to a platform with an installation of Singularity, rather than attempting to run the image using the Docker container.

```bash
$ scp -i /path/to/keyfile/id_mykey ./my_image.sif xli07@ntu.nscc.sg:/home/xli072
```

### Additional Singularity features

* **Remote Builder Capabilities:** If you have access to a platform with Singularity installed but you don’t have root access to create containers, you may be able to use the [Remote Builder](https://cloud.sylabs.io/builder) functionality to offload the process of building an image to remote cloud resources. You’ll need to register for a _cloud token_ via the link on the [Remote Builder](https://cloud.sylabs.io/builder) page.
* **Signing containers:** Singularity supports signing containers. This allows a digital signature to be linked to an image file. This signature can be used to verify that an image file has been signed by the holder of a specific key and that the file is unchanged from when it was signed. You can find full details of how to use this functionality in the Singularity documentation on [Signing and Verifying Containers](https://sylabs.io/guides/3.0/user-guide/signNverify.html).

### Running MPI with Singularity&#x20;



### References

{% embed url="https://singularity-tutorial.github.io/01-installation/" %}

{% embed url="https://epcced.github.io/2021-07-29_Singularity_Online/" %}
