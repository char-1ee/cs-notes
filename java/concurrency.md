---
description: >-
  A collection of concurrency and multi-threaded concepts in Java (though I
  think this post low quality since it is only for interview).
---

# Concurrency

### _volatile_

Ensure memory visibility but cannot ensure atomic operations.

由于 `Java` 内存模型(`JMM`)规定，所有的变量都存放在主内存中，而每个线程都有着自己的工作内存(高速缓存)。

线程在工作时，需要将主内存中的数据拷贝到工作内存中。这样对数据的任何操作都是基于工作内存(效率提高)，并且不能直接操作主内存以及其他线程工作内存中的数据，之后再将更新之后的数据刷新到主内存中。(工作内存 - stack, 主存 - heap)

当一个变量被 `volatile` 修饰时，任何线程对它的写操作都会立即刷新到主内存中，并且会强制让缓存了该变量的线程中的数据清空，必须从主内存重新读取最新数据. 但是volatile 不可以保证原子性。

另一个作用：防止JVM指令重排, 一个经典的使用场景就是双重懒加载的单例模式

```java
public class Singleton {

    private static volatile Singleton instance;
    
    private Singleton {}
    
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (this.class) {
                // avoid memory reordering
                instance = new Singleton(); 
            }
         }
         return instance;
    }
}
```

如果不用 ，`singleton = new Singleton();`，这段代码其实是分为三步：

* 分配内存空间。(1)
* 初始化对象。(2)
* 将 `singleton` 对象指向分配的内存地址。(3)

加上 `volatile` 是为了让以上的三步操作顺序执行，不然有可能第二步在第三步之前被执行就有可能某个线程拿到的单例对象是还没有初始化的，以致于报错。

### _interrupt()_

How to use `thread.interrupt()` to stop a thread.

```java
public class MyThread implements Runnable {
    @Override
    public void run() {
        while (!Thread.currentThread().isInterrupted()) {
            System.out.println("running");
        }
        System.out.println("Exit");
    }
}
```

```java
public static void main(String[] args) {
    MyThread t = new MyThread();
    new Thread(t).start();
    TimeUnit.SECONDS.sleep(5);
    try {
        t.interrupt();
    } catch (Exception e) {
        e.printStackTree();
    }
}
```

### _join()_

```java
public static void main(String[] args) {
    Thread t1 = new Thread(new Runnable() {
        @Override
        public void run() {
            LOGGER.info("running");
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    });
    
    Thread t2 = new Thread(new Runnable() {
        @Override
        public void run() {
            LOGGER.info("running2");
            try {
                Thread.sleep(4000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    });
    
    t1.start();
    t2.start();
    
    t1.join();
    //等待线程2终止
    t2.join();
    LOGGER.info("main over");
}
```
