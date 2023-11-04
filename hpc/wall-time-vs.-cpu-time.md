# Time

### Wall time vs. CPU time

wall-clock-time, user-cpu-time, system-cpu-time

Wall-clock time is the time that a clock on the wall (or a stopwatch in hand) would measure as having elapsed between the start of the process and 'now'.

The user-cpu time and system-cpu time are pretty much as you said - the amount of time spent in user code and the amount of time spent in kernel code.

The wall-clock time is not the number of seconds that the process has spent on the CPU; it is the elapsed time, including time spent waiting for its turn on the CPU (while other processes get to run)

### Time ratio

It’s easier to express the possible relationship as a ratio of `(CPU time) / (wall clock time)`, which is to say `CPU/second`.

If this is a single-threaded process:

1. **`CPU/second ≈ 1`:** The process spent all of its time using the CPU. A faster CPU will likely make the program run faster.
2. **`CPU/second < 1`:** The lower the number, the more of its time the process spent waiting (for the network, or the hard drive, or locks, or other processes to release the CPU, or just sleeping). E.g. if CPU/second is 0.75, 25% of the time was spent waiting.

If this is a multi-threaded process and your computer has `N` CPUs and at least `N` threads, `CPU/second` can be as high as `N`.

1. **`CPU/second < 1`:** The process spent much of its time waiting.
2. **`CPU/second ≈ N`:** The process saturated all of the CPUs.
3. **Other values:** The process used some combination of waiting and CPU, and which is the bottleneck can be harder to tell with just this measurement. You can get some sense by the number of threads—if you’re running 2 threads and `CPU/second = 2`, you know you’re saturated.

### Practice

Say we want to measure the bottleneck of certain operation:

```bash
$ time host -t MX pythonspeed.com
pythonspeed.com mail is handled by in1-smtp.messagingengine.com.
pythonspeed.com mail is handled by in2-smtp.messagingengine.com.

real    0m0.069s
user    0m0.006s
sys     0m0.005s
```

* `real`: the wall clock time.
* `user`: the process CPU time.
* `sys`: the operating system CPU time due to system calls from the process.

So we can easily find that the bottleneck is on network.
