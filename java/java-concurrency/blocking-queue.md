---
description: A typical producer-and-consumer solution.
---

# Blocking Queue

Refer to interface [_BlockingQueue_](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/BlockingQueue.html) _which extends Queue._

```java
public class BlockingQueue {
    private static final int MAX_SIZE = 5;
    private List<Integer> buff;
    
    public BlockingQueue() {
        this.buff = new ArrayList<>();
    }
    
    public synchronized void put(int val) {
        while (buff.size() == MAX_SIZE) {
            try {
                wait();
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
                e.printStackTrace();
            }
        }
        buff.add(val);
        notifyAll(); // notify()
    }
    
    public synchronized void take() {
        while (buff.isEmpty()) {
            try {
                wait();
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
                e.printStackTrace();
            }
        }
        buff.remove(0);
        notifyAll();
    }
}
```

```java
public class Producer implements Runnable {
    private BlockingQueue bq;
    
    public Producer(BlockingQueue bq) {
        this.bq = bq;
    }
    
    @Override
    public void run() {
        // simulate process of producing new items
        while (true) { 
            bq.put(114514);
        }
    }
}
```

```java
public class Consumer implements Runnable {
    private BlockingQueue bq;
    
    public Consumer(BlockingQueue bq) {
        this.bq = bq;
    }
    
    @Override
    public void run() {
        // simulate process of consuming items from queue
        while (true) {    
            bq.take();
        }
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        BlockingQueue bq = new BlockingQueue();
        Producer p = new Producer();
        Consumer c = new Consumer();
        new Thread(p).start();
        new Thread(c).start();
    }
}
```
