# Thread.join()

When we invoke the _join()_ method on a thread, the calling thread goes into a waiting state. It remains in a waiting state until the referenced thread terminates.

```java
class SampleThread extends Thread {
    public int processingCount = 0;

    SampleThread(int processingCount) {
        this.processingCount = processingCount;
        LOGGER.info("Thread Created");
    }

    @Override
    public void run() {
        LOGGER.info("Thread " + this.getName() + " started");
        while (processingCount > 0) {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                LOGGER.info("Thread " + this.getName() + " interrupted");
            }
            processingCount--;
        }
        LOGGER.info("Thread " + this.getName() + " exiting");
    }
}

@Test
public void givenStartedThread_whenJoinCalled_waitsTillCompletion() throws InterruptedException {
    Thread t2 = new SampleThread(1);
    t2.start();
    LOGGER.info("Invoking join");
    t2.join();
    LOGGER.info("Returned from join");
    assertFalse(t2.isAlive());
}
```

Output:

```log
INFO: Thread Created
INFO: Invoking join
INFO: Thread Thread-1 started
INFO: Thread Thread-1 exiting
INFO: Returned from join
```
