# Python: async/await

### Python Coroutine

Coroutine is a Python class, including:

1. Function defined by `async def`. Inside the function can use `await` keyword.
2. Object returned by `async def` decorated function., which can be used for `await` or `asyncio.run`.

### await + coroutine

Concurrently starts multiple coroutines. `asyncio.gather` run them concurrently.

```python
import asyncio
import time

async def async_hello_world():
    now = time.time()
    await asyncio.sleep(1)
    print(time.tim() - now)
    print("Hello world")
    await asyncio.sleep(1)
    print(time.time() - now)
    
async def main():
    await asyncio.gather(async_hello_world(), async_hello_world(), async_hello_world())
    
now = time.time()
asyncio.run(main())
print(f"Total time for running 3 coroutine: {time.time() - now}")
```

### await + task

We want to run concurrently `Task`. When we `await` a Task, event loop starts scheduling runnable tasks at the current timestamp, til awaited Task ends. The event loop could arrange tasks in an arbitrary order.

```python
import asyncio
import time

async def async_hello_world():
    now = time.time()
    await asyncio.sleep(1)
    print(time.time() - now)
    print("Hello, world!")
    await asyncio.sleep(1)
    print(time.time() - now)
    
async def main():
    t1 = asyncio.create_task(async_hello_world())
    t2 = asyncio.create_task(async_hello_world())
    t3 = asyncio.create_task(async_hello_world())
    
    await t1
    await t2
    await t3
```

> 在Python中，存在三类可以用于`await`的对象：
>
> * coroutine对象，创建方法为调用coroutine函数，`await` coroutine对象时直接执行相应的coroutine函数，直到该函数返回
> * future对象，创建方法为`loop.create_future()`，`await` future对象之前一般需要用`loop.call_later`等函数设置它完成的时刻，`await` future对象时当前coroutine将停止执行，直到`future.done() == True`
> * task对象，创建方法为`asyncio.create_task(coroutine)`，该函数会在当前的event loop里新增一个Task。`await` task对象时，当前coroutine将停止执行，直到这个Task执行结束。
>
> 根据以上内容，我们可以看到，`task`和`future`很像。事实上，Python的Task类就是Future类的子类
