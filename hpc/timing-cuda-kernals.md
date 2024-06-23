# Timing CUDA kernals

## TL;DR

```python
import torch
from time import perf_counter
import os
import subprocess

# Setup device and clock speeds
DEVICE = os.environ.get("CUDA_VISIBLE_DEVICES", "0")
CLOCK_SPEED = 1350  # Must choose a clock speed that's supported on your device.

def set_clock_speed():
    subprocess.run(f"sudo nvidia-smi -pm ENABLED -i {DEVICE}", shell=True)
    subprocess.run(f"sudo nvidia-smi -lgc {CLOCK_SPEED} -i {DEVICE}", shell=True)

def reset_clock_speed():
    subprocess.run(f"sudo nvidia-smi -pm ENABLED -i {DEVICE}", shell=True)
    subprocess.run(f"sudo nvidia-smi -rgc -i {DEVICE}", shell=True)

def flush_cache(size_mb=40):
    x = torch.empty(int(size_mb * (1024 ** 2)), dtype=torch.int8, device='cuda')
    x.zero_()

# Kernel simulation function (to be replaced with actual computation)
def run_kernel():
    torch.matmul(torch.randn(1000, 1000, device='cuda'), torch.randn(1000, 1000, device='cuda'))

# Timing with CUDA events
def time_with_events(steps=10):
    start_events = [torch.cuda.Event(enable_timing=True) for _ in range(steps)]
    end_events = [torch.cuda.Event(enable_timing=True) for _ in range(steps)]

    for i in range(steps):
        flush_cache()
        torch.cuda._sleep(1_000_000)  # sleep to saturate the command queue
        start_events[i].record()
        run_kernel()
        end_events[i].record()

    torch.cuda.synchronize()
    return [start.elapsed_time(end) for start, end in zip(start_events, end_events)]

# Using CUDA Graphs
def time_with_cuda_graphs(steps=10):
    start_events = [torch.cuda.Event(enable_timing=True) for _ in range(steps)]
    end_events = [torch.cuda.Event(enable_timing=True) for _ in range(steps)]

    graph = torch.cuda.CUDAGraph()

    with torch.cuda.graph(graph):
        for _ in range(steps * 10):  # capture multiple runs to amortize launch overhead
            run_kernel()

    for i in range(steps):
        start_events[i].record()
        graph.replay()
        run_kernel()  # an additional run to capture end event timing
        end_events[i].record()

    torch.cuda.synchronize()
    return [start.elapsed_time(end) for start, end in zip(start_events, end_events)]

# Main function to orchestrate timing experiments
def main():
    steps = 10
    set_clock_speed()

    # Warm up
    for _ in range(steps):
        run_kernel()

    # Time using CUDA events
    event_times = time_with_events(steps)
    print("CUDA Event Times:", event_times)

    # Time using CUDA graphs
    graph_times = time_with_cuda_graphs(steps)
    print("CUDA Graph Times:", graph_times)

    reset_clock_speed()

if __name__ == "__main__":
    main()
```

## Host-Device Synchronization

Pytorch executes GPU kernels asynchronously. While a CUDA kernel runs on GPU, the CPU continues to queue up further kernels behind it.&#x20;

Call `torch.cuda.synchronize()` before taking a timing measurement. It waits all kernels in all CUDA streams to complete.

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```python
import torch
from time import perf_counter

steps = 10
times = []
sync_times = []

# This won't time the CUDA kernel - only the launch overhead
for _ in range(steps):
    start_time = perf_counter()

    run_kernel() # Some kernel

    end_time = perf_counter()
    times.append(end_time - start_time)

# This measures what we actually care about
for _ in range(steps):
    start_time = perf_counter()

    run_kernel()
    torch.cuda.synchronize() # NOTE: synchronize each a time

    end_time = perf_counter()
    sync_times.append(end_time - start_time)
```

## CUDA Events

We notice that in above code, we explicitly call `torch.cuda.synchronize()` every time it runs the kernel. Thus, we had not only just time kernel execution, but also time the synchronization overhead. Additionally, timed the kernel launch overhead.

So using CUDA Events can avoid unnecessary synchronization points and hide kernel launch overhead.

```python
steps = 10
start_events = [torch.cuda.Event(enable_timing=True) for _ in range(steps)]
end_events = [torch.cuda.Event(enable_timing=True) for _ in range(steps)]

for i in range(steps):
    start_events[i].record() # puts a timestamp in kernel execution stream
    run_kernel()
    end_events[i].record() # puts a timestamp in kernel execution stream

torch.cuda.synchronize()
times = [s.elapsed_time(e) for s, e in zip(start_events, end_events)]
```

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption><p>step = 2</p></figcaption></figure>

When you place a `cuda.Event()` before and after a kernel launch, the events are enqueued into the CUDA stream right alongside the kernel. However, they do not cause the CPU to wait. They only mark the points in time when they reach that position in the stream. Since the timing is handled entirely by the GPU and the events are processed as part of the normal stream execution, there's no additional overhead from the CPU waiting or checking the status. This means that the recorded time pertains purely to GPU activity between the events, excluding the CPU-side overhead of launching the kernel.

## Warm-up Steps

Avoid the overheads during training and inference, including:

* Optimization passes / codegen applied by PyTorch's JIT fuser after the first few input tensors are encountered.
* On-the-fly microbenchmarking carried out by `torch.cudnn.benchmark` when selecting optimal convolution kernel for a given input shape.
* Lazy loading of kernels into the CUDA context with `CUDA_MODULE_LOADING=LAZY` & CUDA 11.7+
* Overhead of `cudaMalloc` calls by PyTorch's caching allocator to initially grow the memory pool, ready for later re-use.

```python
steps = 10

# Warmup steps
for _ in range(steps):
    run_kernel() # don't record time

start_events = [torch.cuda.Event(enable_timing=True) for _ in range(steps)]
end_events = [torch.cuda.Event(enable_timing=True) for _ in range(steps)]

for i in range(steps):
    start_events[i].record()
    run_kernel()
    end_events[i].record()

torch.cuda.synchronize()
times = [s.elapsed_time(e) for s, e in zip(start_events, end_events)]
```

## Fixed Clocks

GPU clock speed can vary significantly according to limits on temperature and power consumption. As such, fixing the clock enables consistent and **reproducible** benchmarking.

```python
import os
import subprocess

DEVICE = os.environ.get("CUDA_VISIBLE_DEVICES")
CLOCK_SPEED = 1350 # Must choose a clock speed that's supported on your device.

def set_clock_speed():
    """
    Set GPU clock speed to a specific value.
    This doesn't guarantee a fixed value due to throttling, but can help reduce variance.
    """
    process = subprocess.Popen("nvidia-smi", stdout=subprocess.PIPE, shell=True)
    stdout, _ = process.communicate()
    process = subprocess.run(f"sudo nvidia-smi -pm ENABLED -i {DEVICE}",      shell=True)
    process = subprocess.run(f"sudo nvidia-smi -lgc {CLOCK_SPEED} -i {DEVICE}", shell=True)

def reset_clock_speed():
    """
    Reset GPU clock speed to default values.
    """
    subprocess.run(f"sudo nvidia-smi -pm ENABLED -i {DEVICE}", shell=True)
    subprocess.run(f"sudo nvidia-smi -rgc -i {DEVICE}", shell=True)
```

One caveat is that selecting a clock speed with `nvidia-smi` doesn't guarantee that your GPU will run at the requested speed. The GPU always retains the ability to decrease the clock rate (throttling) to prevent damage to the hardware. But by setting the clock speed to a value sufficiently below the maximum, we can ensure that throttling is less severe.

## Cache Flush

Ensure that the GPU memory caches are cleared between timing calls. This avoids the possibility of repeated kernel executions exploiting cache hits and artificially reducing latency.

* Pass different input data for each pass. But need to cover all bases.
* If input too large, explicitly flush the cache between passes.

```python
# allocating 40MB to match L2 cache size on A100
x = torch.empty(int(40 * (1024 ** 2)), dtype=torch.int8, device='cuda')

# Overwrite existing cache lines
def flush_cache():
    x.zero_()
```

## Sleep / CUDA Graphs

What if kernel execution is quicker than the kernel launch?

<figure><img src="../.gitbook/assets/image (5) (1).png" alt=""><figcaption><p>GPU overrun CPU</p></figcaption></figure>

The simplest solution is to saturate the command queue prior to launching the target kernel.

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

Launch a sufficiently expensive kernel prior to the operations we are interested in, thus creating a backlog. A cleaner solution is to ask the GPU to wait for a fixed number of instruction cycles, either by using CUDA's `__nanosleep` or `torch.cuda._sleep()` :

```python
set_clock_speed()

steps = 10

# Warmup steps
for _ in range(steps):
    run_kernel()

start_events = [torch.cuda.Event(enable_timing=True) for _ in range(steps)]
end_events = [torch.cuda.Event(enable_timing=True) for _ in range(steps)]

for i in range(steps):
    flush_cache()
    torch.cuda._sleep(1_000_000)

    start_events[i].record()
    run_kernel()
    end_events[i].record()

torch.cuda.synchronize()
times = [s.elapsed_time(e) for s, e in zip(start_events, end_events)]

reset_clock_speed()
```

A second solution is to use CUDA graphs. This minimizes launch overhead by joining a series of independent kernel launches into a single kernel. Note that we execute the target kernel multiple times within the graph capture to amortize the cost of the launch overhead:

```python
set_clock_speed()

steps = 10

# Warmup steps
for _ in range(steps):
    run_kernel()

start_events = [torch.cuda.Event(enable_timing=True) for _ in range(steps)]
end_events = [torch.cuda.Event(enable_timing=True) for _ in range(steps)]

# capture CUDA graph
graph = torch.cuda.CUDAGraph()
with torch.cuda.graph(graph):
    for _ in range(steps*10):
        run_kernel()

for i in range(steps):
    start_events[i].record()
    graph.replay()
    run_kernel()
    end_events[i].record()

torch.cuda.synchronize()
times = [s.elapsed_time(e) for s, e in zip(start_events, end_events)]

reset_clock_speed()
```

## Reference

{% embed url="https://www.speechmatics.com/company/articles-and-news/timing-operations-in-pytorch" %}
