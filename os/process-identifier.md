---
description: Process Identifier
---

# PID

In [Unix-like](https://en.wikipedia.org/wiki/Unix-like) operating systems, new processes are created by the [`fork()`](https://en.wikipedia.org/wiki/Fork\_\(operating\_system\)) [system call](https://en.wikipedia.org/wiki/System\_call). The PID is returned to the [parent process](https://en.wikipedia.org/wiki/Parent\_process), enabling it to refer to the child in further function calls. The parent may, for example, wait for the child to terminate with the [`waitpid()`](https://en.wikipedia.org/wiki/Waitpid\(\)) function, or terminate the process with [`kill()`](https://en.wikipedia.org/wiki/Kill\(\)).

There are two tasks with specially distinguished process IDs: _swapper_ or _sched_ has process ID 0 and is responsible for [paging](https://en.wikipedia.org/wiki/Paging), and is actually part of the kernel rather than a normal [user-mode](https://en.wikipedia.org/wiki/User-mode) process. Process ID 1 is usually the [init](https://en.wikipedia.org/wiki/Init) process primarily responsible for starting and shutting down the system. Originally, process ID 1 was not specifically reserved for init by any technical measures: it simply had this ID as a natural consequence of being the first process invoked by the kernel. More recent Unix systems typically have additional kernel components visible as 'processes', in which case PID 1 is actively reserved for the init process to maintain consistency with older systems.

Process IDs, in the first place, are usually allocated on a sequential basis, beginning at 0 and rising to a maximum value which varies from system to system. Once this limit is reached, allocation restarts at 300 and again increases. In [macOS](https://en.wikipedia.org/wiki/MacOS) and [HP-UX](https://en.wikipedia.org/wiki/HP-UX), allocation restarts at 100. However, for this and subsequent passes any PIDs still assigned to processes are skipped. Some consider this to be a potential security vulnerability in that it allows information about the system to be extracted, or messages to be covertly passed between processes. As such, implementations that are particularly concerned about security may choose a different method of PID assignment.[\[1\]](https://en.wikipedia.org/wiki/Process\_identifier#cite\_note-Nonsequential\_PIDs-1) On some systems, like [MPE/iX](https://en.wikipedia.org/wiki/HP\_Multi-Programming\_Executive), the lowest available PID is used, sometimes in an effort to minimize the number of process information kernel pages in memory.

The current process ID is provided by a `getpid()` system call, or as a variable `$$` in shell. The process ID of a parent process is obtainable by a `getppid()` system call.

On [Linux](https://en.wikipedia.org/wiki/Linux), the maximum process ID is given by the pseudo-file `/proc/sys/kernel/pid_max`.[\[2\]](https://en.wikipedia.org/wiki/Process\_identifier#cite\_note-2)
