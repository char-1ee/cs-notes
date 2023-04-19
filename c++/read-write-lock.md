---
description: implemented by shared_mutex
---

# Read Write Lock

### std::shared\_mutex

[https://en.cppreference.com/w/cpp/thread/shared\_mutex](https://en.cppreference.com/w/cpp/thread/shared\_mutex)

> A shared\_mutex has two levels of access:
>
> * _shared_ - several threads can share ownership of the same mutex.
> * _exclusive_ - only one thread can own the mutex.
>
> If one thread has acquired the _exclusive_ lock (through [lock](https://en.cppreference.com/w/cpp/thread/shared\_mutex/lock), [try\_lock](https://en.cppreference.com/w/cpp/thread/shared\_mutex/try\_lock)), no other threads can acquire the lock (including the _shared_).
>
> If one thread has acquired the _shared_ lock (through [lock\_shared](https://en.cppreference.com/w/cpp/thread/shared\_mutex/lock\_shared), [try\_lock\_shared](https://en.cppreference.com/w/cpp/thread/shared\_mutex/try\_lock\_shared)), no other thread can acquire the _exclusive_ lock, but can acquire the _shared_ lock.
>
> Only when the _exclusive_ lock has not been acquired by any thread, the _shared_ lock can be acquired by multiple threads.
>
> Within one thread, only one lock (_shared_ or _exclusive_) can be acquired at the same time.
>
> Shared mutexes are especially useful when shared data can be safely read by any number of threads simultaneously, but a thread may only write the same data when no other thread is reading or writing at the same time.

```cpp
#include <iostream>
#include <mutex>
#include <shared_mutex>
#include <thread>

class ThreadSafeCounter {
private:
    mutable std::shared_mutex mutex_;
    unsigned int value_ = 0;

public:
    ThreadSafeCounter() = default;
    
    // Multiple threads/readers can read the counter's value at the same time.
    unsigned int get() const {
        std::shared_lock lock(mutex_);
        return value_;
    }
    
    // Only one thread/writer can increment/write the counter's value.
    void increment() {
        std::unique_lock lock(mutex_);
        ++value_;
    }
    
    // Only one thread/writer can reset/write the counter's value.
    void reset() {
        std::unique_lock lock(mutex_);
        value_ = 0;
    }
};
```
