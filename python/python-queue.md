# Queue

Consumer producer model

```python
import threading
import Queue

q = queue.Queue()

def consumer():
    while True:
        t = q.get()    # get() in queue is a block operation
        print("Consume: ", t)
        q.task_done()    # tells a task is complete, counter--

def producer():
    for i in range(10):
        q.put(i)
        
t1 = threading.Thread(target=consumer, args=(q,), daemon=True)
t2 = threading.Thread(target=consumer, args=(q,), daemon=True)
t1.start()
t2.start()

producer(q)
q.join()    # blocks until all items in queue processed
```
