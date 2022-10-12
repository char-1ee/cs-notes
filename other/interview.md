---
description: Tech interview preparation.
---

# Interview

### Algorithms

#### String

* String vs StringBuilder
  * `String` is **immutable**, which means altering the String value will create another String object/instance, and the new temporary instances can be only obtained by method returns or assignments
  * `StringBuilder` is **mutable**, which changes on value can be maintained.
* StringBuilder vs StringBuffer
  * StringBuffer has thread-safety but people realized that it costy to ensure thread safety, thus StringBuilder is promoted.
*   Conversions

    ```java
    // String to StringBuilder and StringBuffer
    String s = "";
    StringBuilder sb = new StringBuilder(s);
    StringBuffer sbr = new StringBuffer(s);

    // StringBuilder and StringBuffer to String
    String s = sb.toString();

    // StringBuffer and StringBuilder
    String tmp = sb.toString();
    StringBuffer sbr = new StringBuffer(tmp);
    ```
*   StringBuilder APIs

    ```java
    StringBuilder sb = new StringBuilder(str);
    sb.append("");
    sb.charAt(int index);     // return char
    sb.indexOf(String str);    // return int
    sb.substring(int start, int end);  // return String
    sb.reverse();       // return StringBuilder
    ```
*   String and Integer conversion

    ```java
    int num_int = Integer.parseInt(string);
    Integer num_Int = Integer.valeuOf(string);
    String string = String.valeuOf(num);
    ```

#### String cpp

*   String concantenation

    ```cpp
    char init[] = "string to append";
    char add[] = "string to be appended";

    std::strcat(init, add);

    string init("string to append");
    string add("string to be appended");

    init.append(add);
    init += add;
    ```

#### [Hash table](https://www.cnblogs.com/three-fighter/p/15130703.html)

*   Hash table in Java: [HashMap](https://docs.oracle.com/javase/8/docs/api/java/util/HashMap.html), [HashSet](https://docs.oracle.com/javase/7/docs/api/java/util/HashSet.html)

    ```java
    Map<K, V> map = new HashMap<>();

    boolean flag = map.containsKey(Object k);
    boolean flag = map.containsValue(Object v);

    map.put(K key, V value);
    map.remove(Object key);

    V value = map.get(Object key);
    V value = map.getOrDefault(Object key, V defaultValue);

    Set<K> keys = map.ketySet();
    Set<Map.Entry<K, V>> entries = map.entrySet<>();
    ```

    ```java
    Set<T> set = new HashSet<>();
    Set<T> set = new HashSet<>(Collection<? extends T> c);

    set.add(T t);
    set.remove(Object o);

    boolean flag = set.contains(Object o);
    Iterator<T> iterator = set.iterator();
    ```
*   Hash function in HashMap

    ```java
    static final int hash(Object key) {
     int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }

    int index = hash & (arrays.length - 1);
    ```
* Hash collision
  1. append linked list (size >= 8, turn into red-black tree)
  2. 线性探测法 (开放地址法, re-hash, 公共溢出区法)

#### Stack and Queue

```java
Stack<T> stack = new Stack<>();
stack.empty();   // is empty
stack.peek();   // retrieve
stack.pop();   // pop
stack.push(T item);   // push
stack.search(Object o); // search from top, starting from 1-index
```

```java
Queue<T> queue = new LinkedList<>();
// enqueue
queue.add(T t);
queue.offer(T t);
// dequeue
queue.remove();
queue.poll();
// retrieve
queue.peek();
queue.element();
```

```java
Deque<T> deque = new ArrayDeque<>();
Deque<T> deque = new LinkedList<>();
deque.isEmpty();
// enqueue
deque.offerLast(T t);  // tail
deque.offerFirst(T t);  // head
// dequeue
deque.pollLast();
deque.pollFirst();
// retrieve
deque.peekLast();
deque.peekFirst();
// find (boolean)
deque.removeFirstOccurrence(Object o);
deque.removeLastOccurrence(Object o);
```

*   Reverse Polish Notation ([RPN](https://zh.wikipedia.org/wiki/%E9%80%86%E6%B3%A2%E5%85%B0%E8%A1%A8%E7%A4%BA%E6%B3%95))

    3 4 - 5 + -> 3 - 4 + 5

    3 4 5 _- -> 3 - 4_ 5

    3 4 - 5 _-> (3 - 4)_ 5

    if operand push,

    if operator, pop, calculate, push result,

    Finally, peek the result

#### Sorting

Refer to notes: quick sort, merge sort, shell sort, bucket sort

#### Union find

```
vector<int> roots;
vector<int> ranks;
```

#### Past questions

* ~~Path Sum~~
* ~~Longest common subsequence~~
* ~~Remove duplicate in linked list~~
* ~~Quick sort (and complexity)~~
* Traversal of Binary trees
* ~~Stack with two queues, queue with two stacks~~
* ~~LRU~~
* LC680, 125, 105, 25
* ~~High frequency: LC3, 21, 25, 55, 121, 206, 232~~
* Review
  * LC93, LC146, LC8, ~~LC329~~, union\_find, dfs, bfs, tree traversals
* Merge sort O(n) and space O(1)
* Rotate image
* Morris traversal

### Java

* **OOP** (Abstraction, Encapsulation, Inheritance, Polymorphism)
* Interface and abstract class
* Java built-in data structures
* **Synchronized (this)**
  *   对象锁， 类锁 / lock on object or class

      类锁：修饰 static method / variables, 因为 static 的 field/method 对于一个 class 来说只有一份

      对象锁：修饰 non-static method / block
  * `synchronized(X.class)` is used to make sure that there is exactly one Thread in the block. `synchronized(this)` ensures that there is exactly one thread per instance.
  * 修饰整个 method, 修饰 block (部分 method) -> synchronized (this) {}
*   **Volatile**

    _the volatile modifier guarantees that any thread that reads a field will see the most recently written value_

    When a share variable is with volatile, any changes to it will be updated into main memory. A normal variable has not a fixed timestamp to be written into main memory.
* **Java 内存模型: 分区, JMM, GC**
  * [https://www.cnblogs.com/dolphin0520/p/3920373.html](https://www.cnblogs.com/dolphin0520/p/3920373.html)
  * Java Memory Model (JMM)
  * 并发编程三概念：
    1. 原子性 (atomic)
    2. 可见性 (volatile)
    3. 有序性 (instruction reorder)
* 类的加载
* Future and promise

### Golang

* GMP model: [https://learnku.com/articles/41728](https://learnku.com/articles/41728)
* GC
* Goroutine

### OS

#### **Cache**

* Place data in cache (Direct mapped, fully associative, set associative)
* Find data in cache (in blocks)
*   Replace data in cache ()

    <img src="https://pic4.zhimg.com/80/v2-b3a48ef239772765bff1eb2c396ad79f_720w.jpg" alt="img" data-size="original">
* Write in Cache and DRAM
  * Wrote in cache and DRAM at the same time
  * Only when data replaced in cache, wrote back in DRAM
  * Queue, data wrote in the queue, and then wrote in DRAM from queue
* Cache coherence

#### **Process**

Process is an instance of a program or a running program. ([link](https://www.cnblogs.com/xiaolincoding/p/13289992.html))

*   Context switch, concurrent but not parallel.

    CPU context: registers and PC (Program Counter).
* New -> ready -> running -> waiting/blocked -> running -> terminate
*   Suspend state (6th state): processes in disk do not occupy physical memory

    If there are lots of processes in block state -> waste of physical memory space. Thus, blocked processes are transferred from physical memory space to disk, when these processes need to execute, transferred from disk to physical memory.

    For example, ctrl+Z in Linux and sleep().

    <img src="https://pic3.zhimg.com/80/9faf605ebd46e68d125f5f5ed76495cc_720w.png" alt="img" data-size="original">
* PCB (Process Control Block). The identifier of existence of a process.
  * A PCB contains: pid, uid, current process state, priority, resource allocation (about memory information, I/O devices), values in CPU registers (for resume executing when re-start).
  *   Ready queue, block queue. Organized in linked list.

      <img src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWdrci5jbi1iai51ZmlsZW9zLmNvbS81NjljMzc1MS04NjQzLTQ2ZTctODUwNS0wYTJjMTFkMjkxOTgucG5n?x-oss-process=image/format,png" alt="就绪队列和阻塞队列" data-size="original">
* Controls of process.
  *   Create.

      Child process own all resources that parent process has. When child process is terminated, inherited resources return to parent process. When parent process is terminated, all child processes are terminated (in Linux, child processes are returned to PID =1).

      * 为新进程分配一个唯一的进程标识号，并申请一个空白的 PCB，PCB 是有限的，若申请失败则创建失败；
      * 为进程分配资源，此处如果资源不足，进程就会进入等待状态，以等待资源；
      * 初始化 PCB；
      * 如果进程的调度队列能够接纳新进程，那就将进程插入到就绪队列，等待被调度运行；
  *   Terminate.

      正常结束、异常结束以及外界干预（信号 `kill` 掉）

      * 查找需要终止的进程的 PCB；
      * 如果处于执行状态，则立即终止该进程的执行，然后将 CPU 资源分配给其他进程；
      * 如果其还有子进程，则应将其所有子进程终止；
      * 将该进程所拥有的全部资源都归还给父进程或操作系统；
      * 将其从 PCB 所在队列中删除；
  *   Wake up.

      如果某个进程调用了阻塞语句 (自我阻塞)，则必有一个与之对应的唤醒语句。

      * 在该事件的阻塞队列中找到相应进程的 PCB；
      * 将其从阻塞队列中移出，并置其状态为就绪状态；
      * 把该 PCB 插入到就绪队列中，等待调度程序调度；
  *   Block.

      当进程需要等待某一事件完成时，它可以调用阻塞语句把自己阻塞等待。而一旦被阻塞等待，它只能由另一个进程唤醒。

      阻塞进程的过程如下：

      * 找到将要被阻塞进程标识号对应的 PCB；
      * 如果该进程为运行状态，则保护其现场，将其状态转为阻塞状态，停止运行；
      * 将该 PCB 插入的阻塞队列中去；

#### **Thread**

Process is a program in execution while thread is a segment of a process, which is designed for parallel execution and share same memory space.

* Pros and cons.
  * There can be multiple threads in a process.
  * Threads can run parallelly.
  * Threads can share resources like addresses and files (but not registers and stack).
  * But, when a thread crashes, other threads in the same process all crash.
*   Process versus Thread

    Process is the resources allocation unit while thread is the CPU scheduling unit.

    * 进程是资源（包括内存、打开的文件等）分配的单位，线程是 CPU 调度的单位；
    * 进程拥有一个完整的资源平台，而线程只独享必不可少的资源，如寄存器和栈；
    * 线程同样具有就绪、阻塞、执行三种基本状态，同样具有状态之间的转换关系；
    * 线程能减少并发执行的时间和空间开销；
      * Thread context switch. Less cost than process context switch coz no switch between user space and kernel space.
    * Implementations.
      * User Thread (one-many, many-one, many-many): controlled by user space
      * Kernel Thread: controlled by kernel
      * Lightweight Process
*   User Thread (many-to-one)

    用户线程的整个线程管理和调度，操作系统是不直接参与的，而是由用户级线程库函数来完成线程的管理，包括线程的创建、终止、同步和调度等。

    ![用户级线程模型](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWdrci5jbi1iai51ZmlsZW9zLmNvbS83ZTI3YzEzOC1kYzFmLTRmOWYtODhlMi1lZGVkOGMzMWIyNGIucG5n?x-oss-process=image/format.png)
*   Kernel Thread (one-to-one)

    内核线程是由操作系统管理的，线程对应的 TCB 自然是放在操作系统里的，这样线程的创建、终止和管理都是由操作系统负责。

    ![内核线程模型](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWdrci5jbi1iai51ZmlsZW9zLmNvbS9lZmNlZGM5Ny1lYjI4LTRjNTYtOWYzOS1lNGE3YzkwZGZkNDQucG5n?x-oss-process=image/format.png)
*   Lightweight Process, LWP

    轻量级进程是内核支持的用户线程，一个进程可有一个或多个 LWP，每个 LWP 是跟内核线程一对一映射的，也就是 LWP 都是由一个内核线程支持。

    ![LWP 模型](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWdrci5jbi1iai51ZmlsZW9zLmNvbS9lN2RhYzc3MS05OTZmLTQ5ZWMtOGRhNy1jZDFkMjU1YjViZjUucG5n?x-oss-process=image/format.png)

    **1 : 1 模式**

    一个线程对应到一个 LWP 再对应到一个内核线程，如上图的进程 4，属于此模型。

    * 优点：实现并行，当一个 LWP 阻塞，不会影响其他 LWP；
    * 缺点：每一个用户线程，就产生一个内核线程，创建线程的开销较大。

    **N : 1 模式**

    多个用户线程对应一个 LWP 再对应一个内核线程，如上图的进程 2，线程管理是在用户空间完成的，此模式中用户的线程对操作系统不可见。

    * 优点：用户线程要开几个都没问题，且上下文切换发生用户空间，切换的效率较高；
    * 缺点：一个用户线程如果阻塞了，则整个进程都将会阻塞，另外在多核 CPU 中，是没办法充分利用 CPU 的。

    **M : N 模式**

    根据前面的两个模型混搭一起，就形成 `M:N` 模型，该模型提供了两级控制，首先多个用户线程对应到多个 LWP，LWP 再一一对应到内核线程，如上图的进程 3。

    * 优点：综合了前两种优点，大部分的线程上下文发生在用户空间，且多个线程又可以充分利用多核 CPU 的资源。

    **组合模式**

    如上图的进程 5，此进程结合 `1:1` 模型和 `M:N` 模型。开发人员可以针对不同的应用特点调节内核线程的数目来达到物理并行性和逻辑并行性的最佳方案

#### CPU scheduling

* When scheduling happens
  * ready queue -> running
  * running -> blocked queue
  * running -> terminated
* Preemptive and Non-preemptive: interruptible and ir-interruptible
* Principles
  * CPU utilization
  * throughput (completed process number per unit time)
  * turning around time = executing time + blocking time
  * waiting time = time in ready queue
  * response time = time from click to first response
* Algorithms
  * First Come First Serve, FCFS: for CPU busy system but not for I/O busy system
  * Shortest Job First, SJF: starvation + aging
  *   Highest Response Ratio Next, HRRN

      ![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWdrci5jbi1iai51ZmlsZW9zLmNvbS9lYTUwNTZiYy01MjMwLTRmMzMtYjg3NS1iZTI2ODEzNTM0ZmEucG5n?x-oss-process=image/format.png)

      * 如果两个进程的「等待时间」相同时，「要求的服务时间」越短，「响应比」就越高，这样短作业的进程容易被选中运行；
      * 如果两个进程「要求的服务时间」相同时，「等待时间」越长，「响应比」就越高，这就兼顾到了长作业进程，因为进程的响应比可以随时间等待的增加而提高，当其等待时间足够长时，其响应比便可以升到很高，从而获得运行的机会
  * Round Robin, RR: quantum
    * 如果时间片设得太短会导致过多的进程上下文切换，降低了 CPU 效率；
    * 如果设得太长又可能引起对短作业进程的响应时间变长。通常时间片设为 `20ms~50ms` 通常是一个比较合理的折中值。
  *   Highest Priority First, HPF

      进程的优先级可以分为，静态优先级或动态优先级：

      * 静态优先级：创建进程时候，就已经确定了优先级了，然后整个运行时间优先级都不会变化；
      * 动态优先级：根据进程的动态变化调整优先级，比如如果进程运行时间增加，则降低其优先级，如果进程等待时间（就绪队列的等待时间）增加，则升高其优先级，也就是**随着时间的推移增加等待进程的优先级**。

      该算法也有两种处理优先级高的方法，非抢占式和抢占式：

      * 非抢占式：当就绪队列中出现优先级高的进程，运行完当前进程，再选择优先级高的进程。
      * 抢占式：当就绪队列中出现优先级高的进程，当前进程挂起，调度优先级高的进程运行。

      但是依然有缺点，可能会导致低优先级的进程永远不会运行。
  *   Multilevel Feedback Queue (HPF + RR), MFQ



      * 设置了多个队列，赋予每个队列不同的优先级，每个**队列优先级从高到低**，同时**优先级越高时间片越短**；
      * 新的进程会被放入到第一级队列的末尾，按先来先服务的原则排队等待被调度，如果在第一级队列规定的时间片没运行完成，则将其转入到第二级队列的末尾，以此类推，直至完成；
      * 当较高优先级的队列为空，才调度较低优先级的队列中的进程运行。如果进程运行时，有新进程进入较高优先级的队列，则停止当前运行的进程并将其移入到原队列末尾，接着让较高优先级的进程运行；

      可以发现，对于短作业可能可以在第一级队列很快被处理完。对于长作业，如果在第一级队列处理不完，可以移入下次队列等待被执行，虽然等待的时间变长了，但是运行时间也会更长了，所以该算法很好的**兼顾了长短作业，同时有较好的响应时间。**

      <figure><img src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWdrci5jbi1iai51ZmlsZW9zLmNvbS8zYjIxNTA5YS03ZWJhLTQ3YTEtYmMxYi1kOTAyYjZjNjRlZmQucG5n?x-oss-process=image/format.png" alt=""><figcaption></figcaption></figure>

#### Process sync

* Race condition -> indeterminate
*   Critical section -> mutual exclusive

    ```c
    do {
     // Entry section
      /* Critical section */
     // Exit section
      /* Remainder section */
    } while (TRUE)
    ```
*   TestAndSet (Atomic operation)

    1. update `old_ptr` as `new`
    2. return old value of `old_ptr`

    ```c
    int TestAndSet(int *old_ptr, int new)
    {
        int old = *old_ptr;
        *old_ptr = new;
        return old;
    }
    ```
*   Lock

    * Spin lock. Busy waiting, for sole processor must have a preemptive scheduler.

    ```c
    typedef struct lock_t {
        int flag;
    } lock_t;

    void init(lock_t *lock) {
        lock->flag = 0;
    }

    void unlock(lock_t *lock) {
        lock->flag = 0;
    }

    void lock(lock_t *lock) {
        while (TestAndSet(&lock->flag, 1) == 1)
            ;
        // critical section
    }
    ```

    * Without spin

    ```c
    type struct lock_t {
        int flag;
        queue_t *q;
    } lock_t;

    void init(lock_t *lock)
    {
        lock->flag = 0;
        queue_init(lock->q);
    }

    void lock(lock_t *lock)
    {
        while (TestAndSet(&lock->flag, 1) == 1)
        {
            // 1. save current thread's TCB
            // 2. insert current TCB into waiting queue
            // 3. set current thread waiting state
            // 4. scheduling
        }
    }

    void unlock(lock_t *lock)
    {
        if (lock->q != NULL)
        {
            // 1. retrieve the head element in queue
            // 2. insert that thread into ready queue
            // 3. set that thread ready state
        }
        lock->flag = 0;
    }
    ```
*   Semaphore

    * 1 sem (an integer which represents #resources)
    * P operation: sem--, if sem < 0, then block
    * V operation: sem++, if sem >= 0, then call a waiting thread/process

    ```c
    type struct sem_t {
        int sem;
        queue_t *q;
    } sem_t;

    void init(sem_t *s, int sem)
    {
        s->sem = sem;
        queue_init(s->q);
    }

    void P(sem_t *s)
    {
        s->sem--;
        if (s->sem < 0)
        {
            // 1. save context
            // 2. current TCB enqueue
            // 3. set current thread wait
            // 4. scheduling
        }
    }

    void V(sem_t *s)
    {
        s->sem++;
        if (s->sem <= 0)
        {
            // 1. retrieve head of waiting queue
            // 2. enqueue current TCB into ready queue
            // 3. set current thread ready state
        }
    }
    ```

    * Semaphore usages
      * mutual exclusively enter critical section
      * event synchronization
*   Consumer-producer problem

    ![生产者-消费者模型](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWdrci5jbi1iai51ZmlsZW9zLmNvbS80NWYzNGZhMC1jNTE0LTQxZWEtYTljNC1iNzkxOGZkMzk3ZDMucG5n?x-oss-process=image/format.png)



    ```c
    #define N 100
    semaphore mutex = 1;
    semaphore emptyBuffers = N;
    semaphore fullBuffers = 0;

    void producer()
    {
        while (TRUE)
        {
            P(emptyBuffers); // emptyBuffers - 1
            P(mutex);
            // add buffer
            V(mutex);
            V(fullBuffers); // fullBuffers + 1
        }
    }

    void consumer()
    {
        while (TRUE)
        {
            P(fullBuffers); // fullBuffers - 1
            P(mutex);
            // remove buffer
            V(mutex);
            V(emptyBuffers); // emptyBuffer + 1
        }
    }
    ```

    ```java
    import java.util.ArrayList;
    import java.util.List;

    public class MBlockingQueue {
        private int maxSize;
        private List<Long> queue;

        public MBlockingQueue (int maxSize) {
            this.maxSize = maxSize;
            this.queue = new ArrayList<>();
        }

        public synchronized void put() {
            while (queue.size() == maxSize) {
                try {
                    wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            Long value = System.currentTimeMillis();
            queue.add(value);
            System.out.println("put:" + value);
            notify();
        }

        public synchronized void take() {
            while (queue.isEmpty()) {
                try {
                    wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            Long value = queue.get(0);
            System.out.println("take" + value);
            queue.remove(0);
            notify();
        }
    }

    class Producer implements Runnable {
        private MBlockingQueue queue;

        public Producer (MBlockingQueue queue) {
            this.queue = queue;
        }

        @Override
        public void run() {
            while (true) {
                queue.put();
            }
        }
    }

    class Consumer implements Runnable {
        private MBlockingQueue queue;

        public Consumer(MBlockingQueue queue) {
            this.queue = queue;
        }

        @Override
        public void run() {
            while (true) {
                queue.take();
            }
        }
    }

    class Main {
        public static void main(String[] args) throws InterruptedException {
            MBlockingQueue queue = new MBlockingQueue(100);
            Producer p = new Producer(queue);
            Consumer c = new Consumer(queue);
            new Thread(p).start();
            Thread.sleep(10);
            new Thread(c).start();
        }
    }
    ```

    <figure><img src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWdrci5jbi1iai51ZmlsZW9zLmNvbS80ZjE3ZWUyNC0xNjMzLTRlMGEtYTkzNS02NTkzZmM3ZTA3MzkucG5n?x-oss-process=image/format.png" alt=""><figcaption></figcaption></figure>
* [Dining philosophers problem](https://www.cnblogs.com/xiaolincoding/p/13346658.html)
  * Semaphore -> but when all take his left, deadlock
  * Add a mutex -> only one can eat every time
  * Indexing -> odd index (take right then take left), even index (take left then right) -> no deadlock
  * maintain the state of philosophers (EATING, THINKING, HUNGRY), only left and right are not in EATING state, can the philosopher eat.

#### [IPC](https://www.cnblogs.com/xiaolincoding/p/13402297.html)

Inter Process Communication

* Pipe & FIFO (cache in kernel)
  * Pipe is one-direction, so need two pipes `|`. Range is limited in parent and child processes.
  *   FIFO. A pipe file created in memory (not in file system), can communication between different processes.

      ```bash
      mkfifo mPipe
      echo "Hello" > mPipe
      cat < mPipe
      ```
  *   System call

      ![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWdrci5jbi1iai51ZmlsZW9zLmNvbS83ZmVmZjRjMC1hMWFjLTQ0MzAtYmVhMi1mZTQxYjA0MjI4NWEucG5n?x-oss-process=image/format,png)

      <img src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWdrci5jbi1iai51ZmlsZW9zLmNvbS9jNmExYTllNy1kY2JjLTRmMjUtOTQ3ZC1mNjdhMjg1ZjA2YjAucG5n?x-oss-process=image/format,png" alt="img" data-size="original">
  * Pipe is low efficient, not suitable for frequent communication between processes.
* Message queue
  * Linked list in kernel, user-defined data block.
  * Lifecycle. Exists until OS closes or release message queue.
  * Cons.
    * Not timely.
    * Not suitable for large scale data transmission. `MSGMAX` and `MSGMNB`.
    * Cost of copying data between kernel and user space.
* Shared memory
* Message pass (mailbox)
* Semaphore
  * Semaphore is a integer counter, used for sync and mutually exclusion between processes on shared memory.
  *   P-V operations. (wait() and signal())

      P operation decrease the S value. when entering critical section, do P operation. When S < 0, process is blocked. Otherwise, can enter the critical section.

      V operation increase the S value. when leaving the critical section, do V operation. When S >= 0, blocked processes are allowed to obtain the lock.

      * **P 操作**会把信号量减去 -1，相减后如果信号量 < 0，则表明资源已被占用，进程需阻塞等待；相减后如果信号量 >= 0，则表明还有资源可使用，进程可正常继续执行。
      * **V 操作**会把信号量加上 1，相加后如果信号量 <= 0，则表明当前有阻塞中的进程，于是会将该进程唤醒运行；相加后如果信号量 > 0，则表明当前没有阻塞中的进程；
*   Signal (Async)

    Notify processes with Signal in exception mode.

    E.g. Ctrl + C -> `SIGINT` end the process, Ctrl + Z -> `SIGTSTP` pause the process but not end.
*   Socket (Over host and In host)

    ```c
    int socket(int domain, int type int protocol)
    ```

    <img src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWdrci5jbi1iai51ZmlsZW9zLmNvbS9iMDU3MmRkOS03YTI1LTQ0OTUtOWIyZS0wZTg1NjM0MTg1ZTMucG5n?x-oss-process=image/format.png" alt="img" data-size="original">

    ![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWdrci5jbi1iai51ZmlsZW9zLmNvbS82NTRjY2Y3NC02MWJkLTQ3OTItYWNlZi1jYWI3ZWUwZGM5YzkucG5n?x-oss-process=image/format.png)
* [Summary](https://www.cnblogs.com/xiaolincoding/p/13402297.html)

#### Deadlock

Two threads/processes(use processes below) wait for each to release the lock.

* There are 4 conditions necessary for the occurrence of a deadlock.
  *   **Mutual Exclusion**

      <img src="https://cdn.jsdelivr.net/gh/xiaolincoder/ImageHost4@main/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F/%E6%AD%BB%E9%94%81/%E4%BA%92%E6%96%A5%E6%9D%A1%E4%BB%B6.png" alt="img" data-size="original">

      Process A and process B cannot own same resource at the same time.
  *   **Hold and Wait**

      <img src="https://cdn.jsdelivr.net/gh/xiaolincoder/ImageHost4@main/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F/%E6%AD%BB%E9%94%81/%E6%8C%81%E6%9C%89%E5%B9%B6%E7%AD%89%E5%BE%85%E6%9D%A1%E4%BB%B6.png" alt="img" data-size="original">

      Process A is waiting for resource hold by C, meanwhile A will not release the resources that A have.
  *   **No preemption**

      <img src="https://cdn.jsdelivr.net/gh/xiaolincoder/ImageHost4@main/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F/%E6%AD%BB%E9%94%81/%E4%B8%8D%E5%8F%AF%E5%89%A5%E5%A4%BA%E6%9D%A1%E4%BB%B6.png" alt="img" data-size="original">

      B cannot preemptively obtain the resources until A releases.
  *   **Circular Wait**

      <img src="https://cdn.jsdelivr.net/gh/xiaolincoder/ImageHost4@main/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F/%E6%AD%BB%E9%94%81/%E7%8E%AF%E8%B7%AF%E7%AD%89%E5%BE%85%E6%9D%A1%E4%BB%B6.png" alt="img" data-size="original">

      The order of two processes to obtain the resources goes into loop.
* Tools to detect deadlocks: `pstack`+ `gdb`, `jstack`
* Solve deadlocks [link](https://www.geeksforgeeks.org/introduction-of-deadlock-in-operating-system/?ref=lbp)
  *   Prevention

      Break one of 4 conditions:

      1. Make resources can be mutually used like read-only file and disk. -> not applicable all the time.
      2. Static allocation. A process will not execute until all resources needed are satisfied before execution. -> lower down the resource utilization.
      3. If a process needs new resources, allocate if have, otherwise release all resources it currently has and re-allocate later. -> introduce a scheduler mechanism
      4. Allocation in order. 给系统的所有资源编号，规定进程请求所需资源的顺序必须按照资源的编号依次进行.
  * Avoidance
    * [Safe state](https://zhuanlan.zhihu.com/p/61221667)
  * Detection
    *   single resource deadlock detection

        ![img](https://pic2.zhimg.com/v2-15d05471cb6d97558eac7c026f2fc7d5\_b.jpg)

        algorithm: find cycle in directed graph -> cycle means deadlock
    *   multiple resources deadlock detection

        <img src="https://pic1.zhimg.com/v2-b7e8ba213e6589be48c923a4b0bb43b4_b.jpg" alt="img" data-size="original">
  * Recovery

### Design pattern

#### Factory pattern

Reference: [https://www.cnblogs.com/yssjun/p/11102162.html](https://www.cnblogs.com/yssjun/p/11102162.html)

**Simple factory pattern.** Client call factory class to create new instance at runtime.

**Factory pattern.** Client call different factory classes every of which implements a same abstract factory class, and produces specific product classes.

**Abstract factory pattern**.

#### Observer pattern

**Observer** is a behavioural design pattern that allows some objects to notify other objects about changes in their state.

Publishers and subscribers. Publishers maintain a list of subscribers and public methods to add and remove subscribers. Subscribers implement an interface with `update()` for `notify()` in publishers to call.

#### Strategy pattern

Encapsulation of algorithms in classes. Better maintained and reused.

Client create an instance of strategy class which apply flexibly according to arguments or conditions given.

#### Singleton pattern

* Let you ensure that a class has only one instance, while providing global access point to that instance.
  * Make the default constructor private, to prevent other objects from using the `new` operator with the Singleton class.
  * Create a static creation method that acts as a constructor. Under the hood, this method calls the private constructor to create an object and saves it in a static field. All following calls to this method return the cached object.
*   Starving approach (Thread safe but waste memory)

    ```java
    public class Singleton
    {
        // reference to an instance of self class
        private static final Singleton INSTANCE = new Singleton();

        // private constructor
        private Singleton() {}

        // provide an accessor to obtain instance
        pubic static Singleton getInstance() {
            return INSTANCE;
        }
    }
    ```
*   Lazy approach (locks but expensive)

    ```java
    public class Singleton
    {
        private static Singleton INSTANCE;

        private Singleton() {}

        public synchronized static Singleton getInstance() {
            if (INSTANCE == null)
                INSTANCE = new Singleton();
            return INSTANCE;
        }
    }
    ```
*   Double checked locking (DCL)

    ```java
    public class Singleton
    {
        private static Singleton INSTANCE;

        private Singleton() {}

        public static Singleton getInstance() {
            if (INSTANCE == null) {
                synchronized (Singleton.class) {
                    if (INSTANCE == null) {
                        INSTANCE = new Singleton();
                    }
                }
            }
            return INSTANCE;
        }
    }
    ```
*   Volatile (_the volatile modifier guarantees that any thread that reads a field will see the most recently written value_, avoid rearrangement of instructions）

    ```java
    public class Singleton
    {
        private static volatile Singleton INSTANCE;

        private Singleton() {}

        public static Singleton getInstance() {
            if (INSTANCE == null) {
                synchronized (Singleton.class) {
                    if (INSTANCE == null) {
                        INSTANCE = new Singleton();
                    }
                }
            }
            return INSTANCE;
        }
    }
    ```
*   DCL : The approach taken here is called double-checked locking (DCL). It exists to prevent race condition between multiple threads that may attempt to get singleton instance at the same time, creating separate instances as a result. It may seem that having the `result` variable here is completely pointless. There is, however, a very important caveat when implementing double-checked locking in Java, which is solved by introducing this local variable.

    ```java
    public final class Singleton {

        // The field must be declared volatile so that double check lock would work
        private static volatile Singleton instance;

        public String value;

        private Singleton(String value) {
            this.value = value;
        }

        public static Singleton getInstance(String value) {
            Singleton result = instance;
            if (result != null) {
                return result;
            }
            synchronized(Singleton.class) {
                if (instance == null) {
                    instance = new Singleton(value);
                }
                return instance;
            }
        }
    }
    ```

    Double-checked locking from wiki ([https://refactoring.guru/java-dcl-issue](https://refactoring.guru/java-dcl-issue)):

    Reduce overhead of acquiring a lock by testing the lock criterion before acquiring it.

    Steps are:

    * Check that the variable is initialized (without obtaining the lock). If it is initialized, return it immediately.
    * Obtain the lock.
    * Double-check whether the variable has already been initialized: if another thread acquired the lock first, it may have already done the initialization. If so, return the initialized variable.
    * Otherwise, initialize and return the variable.

### Network

#### Network model

*   TCP/IP model (Application, Transport, Network, Network Interface)

    ```markdown
          +-------------+-------------------------+
            | Application |  HTTP, FTP, etc         |
            +-------------+-------------------------+
            | Transport   |  TCP, UDP               |
            +-------------+-------------------------+
            | Network     |  IP, ICMP               |
            +-------------+-------------------------+
            | Link        | drivers, interface card |
            +-------------+-------------------------+
    ```
* OSI model (7)
* Layering: each layer implements a service via its own internal-layer actions and services provided by below layer.
* In abstraction (Application, Transport, Network, Data Link, Physical)
  * Application: supporting network applications --> **Message**
    * HTTP, IMAP, SMTP, DNS
  * Transport: process-process data transfer --> **Segment**
    * TCP, UDP
  * Network: routing of datagrams from source to destination --> **Datagram**
    * IP, routing protocols
  * Link: data transfer between neighbouring network elements --> **Frame**
    * Ethernet, PPP, Wi-Fi
  * Physical: bits on the wire

#### [3-way & 4-way handshake](https://www.cnblogs.com/three-fighter/p/14802786.html)

3-way handshake is for establish TCP connection between client and server.

4-way handshake is for cut down the connection.

#### From URL to web page

1. Browser parse URL. Generate HTTP request to web server.
2. DNS resolution
3. Protocol stack (TCP, UDP, IP, ICMP, ARP)

#### TCP

![TCP vs. UDP](https://www.lifewire.com/thmb/OhU9Rn5-Myfpbzjyy98U8UMAMCs=/1235x695/smart/filters:no\_upscale\(\)/tcp-headers-f2c0881ea4c94e919794b7c0677ab90a.jpg)

* Sequence number: random initialize, SYN, increment, solve packet disorder
* Acknowledgement number: SYN expected next time, client receive it to ensure all data before the ACK is well received. Solve packet loss.
* Control flags. ACK, RST, FIN, SYN

![TCP 三次握手](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9jZG4uanNkZWxpdnIubmV0L2doL3hpYW9saW5jb2Rlci9JbWFnZUhvc3QyLyVFOCVBRSVBMSVFNyVBRSU5NyVFNiU5QyVCQSVFNyVCRCU5MSVFNyVCQiU5Qy9UQ1AtJUU0JUI4JTg5JUU2JUFDJUExJUU2JThGJUExJUU2JTg5JThCJUU1JTkyJThDJUU1JTlCJTlCJUU2JUFDJUExJUU2JThDJUE1JUU2JTg5JThCLzE0LmpwZw?x-oss-process=image/format,png)

*   Why three-way handshake? Nor 2-way? Nor 4-way?

    * To prevent old duplicate connection initiation from causing confusion.
    * Prevent duplicate resource allocation due to expired SYN from client

    ![客户端主动关闭连接 —— TCP 四次挥手](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9jZG4uanNkZWxpdnIubmV0L2doL3hpYW9saW5jb2Rlci9JbWFnZUhvc3QyLyVFOCVBRSVBMSVFNyVBRSU5NyVFNiU5QyVCQSVFNyVCRCU5MSVFNyVCQiU5Qy9UQ1AtJUU0JUI4JTg5JUU2JUFDJUExJUU2JThGJUExJUU2JTg5JThCJUU1JTkyJThDJUU1JTlCJTlCJUU2JUFDJUExJUU2JThDJUE1JUU2JTg5JThCLzMwLmpwZw?x-oss-process=image/format,png)

#### HTTP

* HTTP (HyperText Transfer Protocol) State codes
  * _1xx informational response_ – the request was received, continuing process
  * _2xx successful_ – the request was successfully received, understood, and accepted
  * _3xx redirection_ – further action needs to be taken in order to complete the request
  * _4xx client error_ – the request contains bad syntax or cannot be fulfilled
  *   _5xx server error_ – the server failed to fulfil an apparently valid request

      200 OK, 302 Found, 400 Bad Request, 404 Not Found, 500 Internal Server Error, 503 Service Unavailable
*   HTTP header fields

    Host, Content-Length, Connection, Content-Type, Content-Encoding
* HTTP methods: GET, POST, PUT, DELETE
* HTTP/1.1 persistent connection, pipeline, head-of-blocking
*   [HTTP and HTTPS](https://www.geeksforgeeks.org/difference-between-http-and-https/?ref=lbp)

    * In HTTP, URL begins with “http://” whereas URL starts with “https://”
    * HTTP uses port number 80 for communication and HTTPS uses 443
    * HTTPS encrypted plaintext transmission by adding SSL protocol between TCP and HTTP
    * HTTP build connection by TCP 3-way handshake, HTTPS by TCP 3-way handshake + SSL/TLS handshake
    * HTTPS needs SSL Certificates from CA

    ![HTTP 与 HTTPS 网络层](https://cdn.jsdelivr.net/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/19-HTTPS%E4%B8%8EHTTP.png)
*   HTTP/1.1

    * TCP persistent connection and support pipeline transmission
    * But, Head-of-line Blocking, HTTP header occupied too much space.

    HTTP/2

    * Header compression (HPACK algorithm)
    *   Binary Framing (enhance transmission efficiency)

        ![HTTP/1 与 HTTP/2](https://img-blog.csdnimg.cn/img\_convert/98f47d18c604c78e4f52cce45dd7fb92.png)
    * Stream
    *   Multiplexing (reduce latency and enhance connection utilization)

        ![多路复用](https://cdn.jsdelivr.net/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/26-%E5%A4%9A%E8%B7%AF%E5%A4%8D%E7%94%A8.png)
    *   Server push (Server initiatively send information to client rather than passively response)

        <img src="https://img-blog.csdnimg.cn/img_convert/660296aecc940c9bfae59f17f450c75f.png" alt="img" data-size="original">

    HTTP/3

    * HTTP/1.1 中的管道（ pipeline）传输中如果有一个请求阻塞了，那么队列后请求也统统被阻塞住了
    * HTTP/2 多个请求复用一个 TCP 连接，一旦发生丢包，就会阻塞住所有的 HTTP 请求。
    * HTTP/3 把 HTTP 下层的 TCP 协议改成了 UDP

    ![HTTP/1 \~ HTTP/3](https://cdn.jsdelivr.net/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/27-HTTP3.png)

#### REST

[Representational State Tranfer](https://www.zhihu.com/collection/467101293). If an architecture fits REST principle, we call it RESTful.

*   **HTTP methods:**

    ```markdown
    GET（SELECT）：从服务器取出资源（一项或多项）。
    POST（CREATE）：在服务器新建一个资源。
    PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。
    PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）。
    DELETE（DELETE）：从服务器删除资源。
    HEAD：获取资源的元数据。
    OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的。
    ```

    ```markdown
    GET /zoos：列出所有动物园
    POST /zoos：新建一个动物园
    GET /zoos/ID：获取某个指定动物园的信息
    PUT /zoos/ID：更新某个指定动物园的信息（提供该动物园的全部信息）
    PATCH /zoos/ID：更新某个指定动物园的信息（提供该动物园的部分信息）
    DELETE /zoos/ID：删除某个动物园
    GET /zoos/ID/animals：列出某个指定动物园的所有动物
    DELETE /zoos/ID/animals/ID：删除某个指定动物园的指定动物
    ```
*   **Filtering**:

    ```markdown
    ?limit=10：指定返回记录的数量
    ?offset=10：指定返回记录的开始位置。
    ?page=2&per_page=100：指定第几页，以及每页的记录数。
    ?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。
    ?animal_type_id=1：指定筛选条件
    ```
* **Other**:
  * use OAuth2.0 framework.
  * response data formatting in JSON rather than XML.
* **Protocol**: choose HTTPS as protocol between user and API.
*   **Domain**: as can as possible depoly under specific domain name.

    ```markdown
    https://api.example.com
    ```

    If API is simple enought that no further extension, then put them under main domain).

    ```markdown
    https://example.com/api
    ```
*   **Versioning**: put the API version number into URL.

    ```markdown
    https://api.example.com/v1/
    ```
*   **Endpoint**: API stands for a resource, so no verbs in URL, should be nouns. Also, the enpoints should match the column name in databaser tables and using plural nouns.

    ```markdown
    https://api.example.com/v1/zoos
    https://api.example.com/v1/animals
    https://api.example.com/v1/employees
    ```

### Linux

#### [Zombie process vs. Orphan process](https://zhuanlan.zhihu.com/p/363005905)

In UNIX system, normally child process is created by parent process. The termination of child process is a asynchronous progess to its parent process, that is, parent process will never know when its child process terminates. When a process complete its job, its parent process must call `wait()` or `waitpid()` to get termination status of the child process.

In this context, we have **zombie process** and **orphan process.**

* Zombie process: A child process is created by `fork()`. If the child process exits but its parent process does not call `wait()` or `waitpid()` to get child process status, then child process's process identifier stays in system. Such child process is called Zombie process. Another definition: any child process (except `init`) after `exit()`, will not disappear immediatly, but leave a data strcuture called Zombie process, waiting for parent process's further handling.
* Orphan process: A parent process exits, but one or some of its child processes keep running. Then these child processes are called orphan process. Orphan process is adapted by `init` process and `init` process finish remaining collection jobs for them.

UNIX has a machanism for parent process to get termination status of child process as parent process wanted. That is, when a process exits, kernel releases all resources of that process, including IO, memory etc. But some information of that process remains, like the process ID (PID), the termination status of the process, the amount of CPU time taken by the process, which are not released until its parent process call `wait()` or `waitpid()`.

Then here comes the problem. If no `wait()` or `waitpid()` is called, the child process's meta information will not be released, then that PID keeps occupied by the child process, or zombie process. However, the number of process ID is limited in system. Thus, if there are plenty of zombie processes wandering around or unmanaged, system cannot create new process because of lack of process ID. So this is hazard of zombie process.

So how to avoid such hazard? Zombie process is not the root of problem, but the parent process of zombie process is. To eliminate large amount of zombie processes, we need to kill the parent process who is continuously creating zombie processes by `SIGKILL` or `SIGTERM`. After that parent process is killed, all zombie processes it produced become orphan processes, which will be taken charge by `init` process. `init` process will `wait()` for these orphan processes and then release resources they occupied.

#### [Linux kernel vs. Windows kernel](https://www.cnblogs.com/xiaolincoding/p/14419269.html)

* kernel space and user space (connected by system calls)

![img](https://cdn.jsdelivr.net/gh/xiaolincoder/ImageHost4@main/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F/%E5%86%85%E6%A0%B8/systemcall.png)

* Linux kernel design ideas
  * Multitask -> concurrent and parallel
  * SMP (Symmetric multiprocessing) -> CPU equivalence
  *   ELF (Executable and Linkable Format):

      Compiler -> Assembler -> Linker -> ELF file

      Load into main memory and read by CPU to execute
  *   Monolithic Kernel

      Linux kernel is a complete executable program and has the highest permission.

      All modules like memory management, file system, device driver, process scheduler run in monolithic kernel.

      <img src="https://cdn.jsdelivr.net/gh/xiaolincoder/ImageHost4@main/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F/%E5%86%85%E6%A0%B8/OS-structure2.png" alt="分别为宏内核、微内核、混合内核的操作系统结构" data-size="original">
*   对于内核的架构一般有这三种类型：

    * 宏内核，包含多个模块，整个内核像一个完整的程序；monolithic kernel
    * 微内核，有一个最小版本的内核，一些模块和服务则由用户态管理；microkernel
    * 混合内核，是宏内核和微内核的结合体，内核中抽象出了微内核的概念，也就是内核中会有一个小型的内核，其他模块就在这个基础上搭建，整个内核是个完整的程序；hybrid kernel

    Linux 的内核设计是采用了宏内核，Window 的内核设计则是采用了混合内核。
* 这两个操作系统的可执行文件格式也不一样， Linux 可执行文件格式叫作 ELF，Windows 可执行文件格式叫作 PE (.exe, .dll, .sys)

#### Shell

* File management
  * Careful!
    * Just `path` is a file or directory named `path` in the current directory.
    * `./path` is a file or directory named `path` in the current directory, with the directory spelled out. The dot directory `.` represents the current directory, and `path` is the name of the file or directory within this directory.
    * `~/path` is a shorthand for `$HOME/path` where `$HOME` is a variable which refers to your home directory. Typically your home directory will be somewhere like `/home/you` or `/Users/you` where `you` is your account name. (The command `echo "$HOME"` will display your home directory.) The expanded value is an absolute path (unless you have messed up the value of `$HOME` thoroughly), as indicated by the initial slash.
    * `/path` is an absolute path which refers to a file or directory named `path` which is in the _root_ directory `/`. Every file on Unix is ultimately somewhere in the directory tree which starts with the root directory
  * ls, ls -l, ls -a
  * cp, mv
  * symlinks
  * `grep [options] pattern [files]`: pattern -> regex pattern (`^` start of a line, `$` end of a line)
    * case sensitive search: `grep -i "Unix" file.txt`
    * display the number of matches: `grep -c "unix" file.txt`
    * display the file names that matches the pattern: `grep -l "unix" f1 f2 f3 f4`
    * display the matched pattern" `grep -o "unix" file`
    * show the line number while displaying the output: `grep -n "unix" file`
  * find, wc
  * cut, cat, sort
* I/O redirection
  * file descriptor: stdin -> 0, stdout -> 1, stderr -> 2
  * `>` : write or rewrite frim left to right
  * `>>`: append stdout into stdin
  * `2> /dev/null`
  * `&>`: both stdout and stderr
  * `<` : redirect to stdin
  *   `<<` : multiple line stdin redirection

      ```bash
      cat <<END > output.txt
      ```
  * `{stdout} | {stdin}`: piping
* Process & System resource management
  * `firefox &`: start a program
  * `pidof firefox`: check pids of a program
  * `ps -p <pid>` : detail info about a pid
  * `ps -ef | grep firefox` : find all firefox related processes
  * `kill -l`: list all signals
  * `kill -9 <pid>`: kill a process with SIGKILL(9) or SIGTERM(15)
  * `pstree [option] [pid or username]` : recursive tree view of processes
  * `top`
    * filter by process:`o` -> `COMMAND=firefox`
    * go back to normal panel: `=`
    * kill a process: `k` -> `<pid>`
    * change updating speed: `s`
    * sort by memory consuming rate: `shift + m`
    * sort by CPU consuming rate: `shift + p`
    * quit: `q`
    * load average
    * In `top` manpage, what are `free`, `used`, `buff`? ([link](https://unix.stackexchange.com/questions/390518/what-do-the-buff-cache-and-avail-mem-fields-in-top-mean))
  * `free -h`: check available and used RAM
    * `total`: RAM
    * `used`: not all used memory shown here
    * `free`: free memory, usually should be small since free means wasted
    * `available`: used to start new applications
    * `buff/cache` : buffer (data being written) + cache (data been read)
* Networking basics
  * `ssh username@<ip>`
  * `ifconfig`: display assigned IP address
  *   `ping <ip/domain name>`

      The ping command sends a request to destination IP or domain name and wait for the reply if request is received by other party it will respond back with ICMP (Internet Control Message Protocol) echo reply.

      The `ping` command will continue to send ICMP packages to the Destination IP address until it receives an interrupt. To stop the command, just hit the `Ctrl+C` key combination.

      E.g. A way to check DNS problems is to ping domain name and ping its IP address separately. If there is no problem, two ping processes will work well. Otherwise, DNS has problems.
  * `route -n` : kernel IP routing table
  * `netstat`: powerful tool for network states
  * `host <domain name>` : DNS lookup operations

### SRE

*   What is SRE?

    SRE, site reliability engineering. SRE is to help build and maintain high scalable and high performance software system. Normally, SREr is responsible for how code is deployed, configured and monitored, as well as the system latency, availability and so on. SRE in different company plays a different role, which is determined by company's business.
*   What is the difference between SRE and DevOps?

    DevOps: manage SDLC, automated operations and deployment, focus on efficiency of delivering the products.

    SRE: focus on system scalability and availability.

    Both care about changes and accidents in SDLC.
* What will you do as an SRE do when customer report a bug?
  * when detected, evaluate the accident. If not vital one, just hot-fix / put into queue; if P0 accident, assembly developers, operations, QAs
  * report to boss, locate the problem, redirect data to host-stand-by, or give permanent solutions like permanent site
  * while the teams work on accident, continuous follow-ups
  * after the accident, record, revise, give compensation mechanism to customers
* How to detect bugs before customers report?
  * Automation tests: unit testing, stress testing, performance test
  * Automation tools: CI/CD
  * Monitor tools: err log / API performance metrics

### System design

#### Maintain login status

* Cookie + Session.
* Security token.
  * Initially login, client posts username and password to server, then server generates a token, which includes user information used for authentication. Server sends back the token to client, client saves the token locally and every time includes the token in HTTP request (HTTP header) to server. We also can set an expire period for the token.
  * Token is stored in string, saving memory space for server. And it is relatively more secure. Even if it is hijacked during transmission, others cannot crack the content, and it reduces server pressure and frequent database queries.

#### MQ

[link1](https://zhuanlan.zhihu.com/p/52773169), [link2](https://www.zhihu.com/question/54152397/answer/1802083263)

### Logic problems

* Bash game. [link1](https://suool.blog.csdn.net/article/details/15777651?utm\_medium=distribute.pc\_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.control\&dist\_request\_id=ea4f2841-87a2-4c48-a15d-b529f81dffd9\&depth\_1-utm\_source=distribute.pc\_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.control), [link2](https://www.zhihu.com/search?type=content\&q=%E5%8F%96%E7%89%A9%E5%BF%85%E8%83%9C%E7%AD%96%E7%95%A5), [link3](https://blog.csdn.net/weixin\_41847115/article/details/82872964)
* Bytedance. [link](https://mp.weixin.qq.com/s?\_\_biz=MzA5NDIzNzY1OQ==\&mid=2735622041\&idx=1\&sn=02b60473a8d2db07dd353aef5e3f294f\&chksm=b6ab412d81dcc83bf0f0f5fd10b1f0ce65e61b78792ba80f0b0f4124fec0e0c1590a1cd4bf92#rd)

### Questions from interviewer

1. What do you know about SRE?
2. Project experiences on SRE topics?
   * Android (networking)
     * client-server model
     * TCP/UDP, socket
   * csh
     * the idea
     * shell
   * bank services:
     * http, restful, API
     * Docker
     * auto test with GitHub Actions
3.  Internship experiences?

    IoT (introducing the function of the app), Agile (meeting, scrum master + developer), Group, divide workload, what I do rather than what is it

    MQTT -> pattern

    project: focus/result -> task divide and workload -> actions (what I did) -> what is the final result

    don't panic -> first of all, second, third (think out of loud)

    SQLite -> why SQLite (easy, native, light-weight) -> why not MySQL, what is the difference between MySQL and SQLite

    Transactions -> security -> hashing MD5

    Organized -> slow and soft

    Don't interrupt interviewer !

### Questions to interviewer

1. I feel like there is no accuracy definitions for an SRE role, may I know what will a SRE do in Tiktok?
2. How a day of SRE in Tiktok be like?
3. What will SRE intern do? In what department?
4. Will SRE intern have some coding tasks like developing some automation tools?
5. Is SRE position on-call? Tight schedule?
6. Are there mentorship for interns?

### Others

* SQLite
* DAO (Data access object)
* Chatting room (Socket)
