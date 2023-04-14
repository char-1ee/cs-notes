# System admin

### Print info of current node (not the whole cluster)

```bash
# Print number of processing unit available
$ nproc --all

# About the processor
$ lscpu

# Show disk free space. 
# -h flag renders the sizes, i.e., GB
# -T shows what kind of filesystem each resource is
$ df -Th

# Print available memory
$ free -m

# Read from /proc
$ cat /proc/cpuinfo
$ cat /proc/meminfo

# System monitor
$ htop

# View process
$ ps ux

# Show disk usage 
$ du -sh <filename>
```

### Run jobs

```bash
# Note that this is running on login node 
$ nano test.sh
$ chmod 744 test.sh
$ ./test.sh
```

### Power managements

```bash
$ systemctl halt
$ systemctl power-off
$ shutdown
$ systemctl reboot

$ systemctl suspend     # save current system state to RAM
$ systemctl hibernate   # save current system state to disk
```

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>
