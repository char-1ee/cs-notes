# Timing

## CPU time

### Wall time vs. CPU time

wall-clock-time, user-cpu-time, system-cpu-time

Wall-clock time is the time that a clock on the wall (or a stopwatch in hand) would measure as having elapsed between the start of the process and 'now'.

The user-cpu time and system-cpu time are pretty much as you said - the amount of time spent in user code and the amount of time spent in kernel code.

The wall-clock time is not the number of seconds that the process has spent on the CPU; it is the elapsed time, including time spent waiting for its turn on the CPU (while other processes get to run)

### Total CPU time

Total CPU Time = User Time (usr) + System Time (sys)

The total CPU time does not include Idle Time, which is the time the CPU might spend idle or time spent waiting for I/O operations to complete if these do not involve the CPU.

### Time ratio

It’s easier to express the possible relationship as a ratio of `(Total CPU time) / (wall clock time)`, which is to say `CPU/second`.

If this is a single-threaded process:

1. **`CPU/second ≈ 1`:** The process spent all of its time using the CPU. A faster CPU will likely make the program run faster.
2. **`CPU/second < 1`:** The lower the number, the more of its time the process spent waiting (for the network, or the hard drive, or locks, or other processes to release the CPU, or just sleeping). E.g. if CPU/second is 0.75, 25% of the time was spent waiting.

If this is a multi-threaded process and your computer has `N` CPUs and at least `N` threads, `CPU/second` can be as high as `N`.

1. **`CPU/second < 1`:** The process spent much of its time waiting.
2. **`CPU/second ≈ N`:** The process saturated all of the CPUs.
3. **Other values:** The process used some combination of waiting and CPU, and which is the bottleneck can be harder to tell with just this measurement. You can get some sense by the number of threads—if you’re running 2 threads and `CPU/second = 2`, you know you’re saturated.

### Practice 1

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

So we can easily find that the bottleneck is on the network.

### Practice 2

When running inference script on a 8xGPU node, the \`time\` result shows:

```bash
real    1m15.683s
user    5m58.835s
sys     3m7.908s
```

We can notice that the total CPU time is 9m67s, which is almost 8 times (7.22) of real-time. This indicates that the running script is utilizing multi-threading and multi-core processing, which is highly parallelized.&#x20;

A key observation is that since the real-time is the total elapsed time, it includes not only the CPU time but also the overheads (I/O waiting, data moving between CPU and GPU, synchronization between CPU and GPU). So, only 8 times of parallelism is not enough to reduce real-time to the current value, because, real-time = CPU time + overhead (overhead > 0), you need to count in overhead time. Therefore, it indicates that there could be even higher parallelism (>8).&#x20;

> Overlapping Execution of CPU and GPU
>
> • Asynchronous Behavior: GPU operations are typically asynchronous to CPU operations. When you offload tasks to the GPU, the CPU can continue executing other tasks. This asynchronous behavior means that the CPU might not be “busy” with computations during the GPU’s active processing time, but the real time (wall clock time) will still be ticking until all tasks, including those on the GPU, are complete.
>
> • Synchronization Points: In most programs that utilize GPUs, there will be points in the code where the CPU must wait for the GPU to finish processing (e.g., waiting for a deep learning model to finish an inference task). These synchronization points will cause the CPU to idle until the GPU tasks are completed, directly impacting the real time.

When the program uses a GPU, operations might be offloaded to it, and the program continues running on the CPU. The actual synchronization point (where the CPU waits for the GPU to finish) might happen behind the code. This can make it difficult to intuitively understand the impact of GPU operations on the total runtime without proper profiling.

Therefore, even though the time command does not explicitly measure how long the GPU takes, the GPU’s processing time contributes to the length of the real-time if there is any form of synchronization or dependency between the CPU and GPU tasks.

## GPU time

To accurately measure and analyze GPU time for applications, we need specialized GPU profiling tools.&#x20;

### NVIDIA GPUs

1.  **NVIDIA Nsight Systems**:

    **Nsight Systems** offers a comprehensive system-wide performance analysis tool that includes detailed GPU metrics. It's part of the NVIDIA Nsight suite and provides insights into GPU activity, helping identify bottlenecks and optimize performance.

    ```bash
    nsys profile --stats=true your_program
    ```
2.  **NVIDIA Nsight Compute**:

    **Nsight Compute** is used for kernel-level profiling for CUDA applications. It provides detailed performance data for each CUDA kernel that executes on the GPU.

    ```bash
    nv-nsight-cu-cli your_program
    ```
3.  **nvprof**:

    **nvprof** is a command-line profiler available in older versions of CUDA (deprecated in newer versions, replaced by Nsight Compute). It provides detailed timing information about GPU kernels and the use of CUDA APIs.

    ```bash
    nvprof your_program
    ```

### AMD GPUs

1. **Radeon GPU Profiler (RGP)**:
   * Although it has a GUI, RGP can be used to generate detailed reports from the command line. It's suitable for profiling applications running on AMD Radeon GPUs, providing insights into GPU performance and workload characteristics.
   * Command-line usage involves setting up the environment to capture the profiling data, which can then be viewed in the GUI.
2.  **ROCm Profiler**:

    The ROCm platform offers tools for profiling AMD GPUs, suitable for applications using the ROCm software stack.

    ```bash
    rocm-profiler your_program
    ```

### Intel GPUs

1. **Intel VTune Profiler**:
   * VTune Profiler supports GPU performance analysis among its many features. It can profile Intel GPUs and provide detailed insights into both CPU and GPU performance.
   * While primarily GUI-based, VTune can be operated from the command line to collect data.
2. **Intel Graphics Performance Analyzers (GPA)**:
   * This suite includes tools for optimizing performance of applications running on Intel graphics systems. Like VTune, it is GUI-oriented but supports command-line data collection.
