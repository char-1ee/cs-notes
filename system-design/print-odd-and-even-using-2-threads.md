---
description: >-
  Refer to original post:
  https://www.baeldung.com/java-even-odd-numbers-with-2-threads
---

# Print odd and even using 2 threads

### Using _wait()_ and _notify()_

```java
class WorkerThread implements Runnable {
    private int max;
    private Printer printer;
    private boolean isEven;
    
    public WorkerThread(int max, Printer printer, boolean isEven) {
        this.max = max;
        this.printer = printer;
        this.isEven = isEven;
    }
    
    @Override
    public void run() {
        int cnt = isEven ? 2 : 1;
        while (cnt <= max) {
            if (isEven) Printer.printEven(cnt);
            else Printer.printOdd(cnt);
            cnt += 2;
        }
    }
}
```

```java
class Printer {
    private volatile boolean isOdd;
    
    public static synchronized void printEven(int num) {
        while (!isOdd) {
            try {
                wait();
            } catch (InterruptException e) {
                Thread.currentThread().interrupt();
            }
        }
        System.out.println(num);
        isOdd = false;
        notify();
    }
    
    public static synchronized void printOdd(int num) {
        while (isOdd) {
            try {
                wait();
            } catch (InterruptException e) {
                Thread.currentThread().interrupt();
            }
        }
        System.out.println(num);
        isOdd = true;
        notify();
    }
}
```

```java
public static void main(String[] args) {
    Printer printer = new Printer();
    new Thread(new WorkerThread(printer, 10, false)).start();
    new Thread(new WorkerThread(printer, 10, true)).start();
}
```

### Using Semaphores

A semaphore controls access to a shared resource through the use of a counter. If the **counter is greater than zero, then access is allowed**. If it is zero, then access is denied.

Java provides the _Semaphore_ class in the _java.util.concurrent_ package and we can use it to implement the explained mechanism. More details about semaphores can be found [here](https://www.baeldung.com/java-semaphore).

We create two threads, an odd thread, and an even thread. The odd thread would print the odd numbers starting from 1, and the even thread will print the even numbers starting from 2.

Both the threads have an object of the _SharedPrinter_ class. **The **_**SharedPrinter**_** class will have two semaphores, **_**semOdd**_** and **_**semEven**_** which will have 1 and 0 permits to start with**. This will ensure that odd number gets printed first.

We have two methods _printEvenNum()_ and _printOddNum()._ The odd thread calls the _printOddNum()_ method and the even thread calls the _printEvenNum()_ method.

To print an odd number, the _acquire()_ method is called on _semOdd_, and since the initial permit is 1, it acquires the access successfully, prints the odd number and calls _release()_ on _semEven._

Calling _release()_ will increment the permit by 1 for _semEven_, and the even thread can then successfully acquire the access and print the even number.

```java
class SharedPrinter {
    private Semaphore semEven = new Semaphore(0);
    private Semapohre semOdd = new Semaphore(1);
    
    public void printEven(int num) {
        try {
            semEven.acquire();
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        System.out.println(num);
        semOdd.release();
    }
    
    public void printOdd(int num) {
        try {
            semOdd.acquire();
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        System.out.println(num);
        semEven.release();
    }
}
```

```java
class Even implements Runnable {
    private SharedPrinter sp;
    private int max;
    
    @Override
    public void run() {
        for (int i = 2; i <= max; i += 2) {
            sp.printEven();
        }
    }
}

class Odd implements Runnable {
    private SharedPrinter sp;
    private int max;
    
    @Override
    public void run() {
        for (int i = 1; i <= max; i += 2) {
            sp.printOdd();
        }
    }
}
```

```java
public static void main(String[] args) {
    SharedPrinter sp = new SharedPrinter();
    Thread oddThread = new Thread(new Odd(), "Odd");
    Thread evenThread = new Thread(new Even(), "Even");
    
    oddThread.start();
    evenThread.start();
}
```
