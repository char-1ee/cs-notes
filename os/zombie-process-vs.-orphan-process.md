---
description: https://zhuanlan.zhihu.com/p/363005905
---

# Zombie process vs. Orphan process

### What is Zombie process and Orphan process?

In UNIX system, normally child process is created by parent process. The termination of child process is a asynchronous progess to its parent process, that is, **parent process will never know when its child process terminates**. When a process complete its job, its parent process must call `wait()` or `waitpid()` to get termination status of the child process.

In this context, we have **zombie process** and **orphan process.**

* Zombie process: A child process is created by `fork()`. If the child process exits but its parent process does not call `wait()` or `waitpid()` to get child process status, then child process's process identifier stays in system. Such child process is called Zombie process. Another definition: any child process (except `init`) after `exit()`, will not disappear immediatly, but leave a data strcuture called Zombie process, waiting for parent process's further handling.
* Orphan process: A parent process exits, but one or some of its child processes keep running. Then these child processes are called orphan process. Orphan process is adapted by `init` process and `init` process finish remaining collection jobs for them.

### Be careful about Zombie processes

UNIX has a machanism for parent process to get termination status of child process as parent process wanted. That is, when a process exits, kernel releases all resources of that process, including IO, memory etc. But some information of that process remains, like the process ID (PID), the termination status of the process, the amount of CPU time taken by the process, which are not released until its parent process call `wait()` or `waitpid()`.

Then here comes the problem. If no `wait()` or `waitpid()` is called, the child process's meta information will not be released, then that **PID keeps occupied by the child process**, or zombie process. However, the number of process ID is limited in system. Thus, if there are plenty of zombie processes wandering around or unmanaged, system cannot create new process because of lack of process ID. So this is hazard of zombie process.

So how to avoid such hazard? Zombie process is not the root of problem, but the parent process of zombie process is.&#x20;

To eliminate large amount of zombie processes, we need to kill the parent process who is continuously creating zombie processes by `SIGKILL` or `SIGTERM`. After that parent process is killed, all zombie processes it produced become orphan processes, which will be taken charge by `init` process. `init` process will `wait()` for these orphan processes and then release resources they occupied.

### Manually kill Zombie process and Orphan process

For Zombie process

```bash
# Find zombie process (status 'Z')
ps aux | grep 'Z'

# Find its parent process and identity the reason 
#    - Option 1: Fix parent process 
#    - Option 2: Reboot (when zombie process too many)
#    - Option 3: Terminate parent process (handle to init)
#    - Option 4: Terminate zombie process

# For option 4
kill -9 <zombie_process_PID>
```

For Orphan process

```bash
# Find orphan process by finding whose PPID = 1(init)
ps aux | awk '$4 == 1'

# Option 1: keep running cos many orphan process harmless
# Option 2: terminate with SIGTERM
kill <orphan_process_PID>

# Notable: SIGKILL terminate process by force without clean resources,
#          so regard SIGKILL as a last resort
```
