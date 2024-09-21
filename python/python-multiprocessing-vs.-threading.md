# Python: multiprocessing vs. threading

* `threading`: Enables creation of multiple threads within a single process. Threads share same memory space and resources of the parent process.
* `multiprocessing`: Allows the creation of multiple processes, each with its own memory space and Python interpreter.&#x20;

### Key differences

#### 1. Concurrency vs. Parallelism

* `threading`
  * Concurrency: only one thread executes Python bytecode at a time due to GIL.
  * Best for I/O bound tasks, e.g. file I/O, network requests.
* `multiprocessing`
  * Parallelism: multiple processes can execute simultaneously on multiple CPU cores, _bypassing the GIL_.
  * Best for CPU bound tasks.

#### 2. Communication

* `threading`: Shared memory allows threads to communicate by modifying shared variables. But need synchronization primitives (Locks, Semaphores, Events, Conditions) to prevent race condition.
* `multiprocessing`
  * Data sharing requires IPC (Pipes, Queues, Mailbox).
  * Synchronization is handled using the library's own synchronization primitives.&#x20;

#### 3. Performance

* `threading`: Lower overhead in creating and managing threads. Beneficial for I/O bound tasks.
* `multiprocessing`: Higher overhead due to the eed to create seperate processes and handle IPC. But can leverage multiple cores.

### Examples

Threading: web scraper

```python
import requests
import threading
from typing import List

def download_from_site(url: str):
    print(f"Downloading from {url}")
    response = requests.get(url)
    print(f"Downloaded {len(response.content)} bytes from {url}")

def download(sites: List):
    threads = []
    for url in sites:
        thread = threading.Thread(target=download_from_site, args=(url,))
        threads.append(thread)
        thread.start()

    for thread in threads:
        thread.join()

if __name__ == "__main__":
    sites = [
        "https://www.jython.org",
        "http://olympus.realpython.org/dice",
        "https://realpython.com"
    ] * 3
    download(sites)
```

Multiprocessing: number cruncher

```python
import multiprocessing
from typing import List

def cpu_bound_task(number: int):
    return sum(i * i for i in range(number))

def find_sums(numbers: List):
    with multiprocessing.Pool() as pool:
        pool.map(cpu_bound_task, numbers)

if __name__ == '__main__':
    numbers = [5_000_000 + x for x in range(20)]
    find_sums(numbers)
```

### In other threading models (w/o GIL)

* Threading in non-GIL languages:
  * Can utilize multiple CPU cores simultaneously
  * Achieves true parallelism for both CPU-bound and I/O-bound tasks
  * Threads can run concurrently on different cores
* Threading in Python (with GIL):
  * Limited to one thread executing Python bytecode at a time
  * Achieves concurrency but not parallelism for CPU-bound tasks
  * Effective for I/O-bound tasks, but not for CPU-bound tasks

However, in languages without GIL, threading model and processing model are also suitable for different use cases:

* Threading model
  * Fine-grained parallelism with shared state
  * I/O-bound tasks with lots of concurrent operations
  * GUI applications for responsiveness
* Processing model
  * CPU-bound tasks that benefit from true parallelism
  * Improved fault isolation
  * Scaling across multiple machines
