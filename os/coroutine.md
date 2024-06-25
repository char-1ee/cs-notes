# Coroutine

In the scenario that we need lots of threads to access a DB, these threads will introduce heavy context switches during IO and fully occupied RAM. Then it is where coroutine comes into place.&#x20;

1. Coroutine runs on top of thread in a way of time slicing.
2. OS is not aware of the existence of the coroutine. So when a coroutine starts blocking IO, OS will block the thread that the current coroutine is on, such that all coroutines on that thread will be blocked :(&#x20;
3.  Then, how can we address if we want to call blocking IO operations in a coroutine?

    > 1. 在调用阻塞IO操作的时候，重新启动一个线程去执行这个操作，等执行完成后，协程再去读取结果。这其实和多线程没有太大区别。
    > 2. 对系统的IO进行封装，改成异步调用的方式，这需要大量的工作，最好寄希望于编程语言原生支持



If we face heavy IO, replace threads with coroutine. Do not call blocking IO in coroutine (print, access files, socket etc.), coroutine is designed for IO intensive tasks.



