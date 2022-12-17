# System monitor

Print info of current node (not the whole cluster)

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
```
