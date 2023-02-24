---
description: Double check locking
---

# Singleton class with DCL

### Double Check Locking

Double check locking is a technique used in computer programming to reduce the overhead of acquiring locks when implementing thread-safe code. It is often used in situations where the creation of a shared resource is expensive, and the resource is accessed frequently by multiple threads.

The basic idea behind double check locking is to minimize the overhead of acquiring a lock by performing a quick check of a shared variable to determine if it has already been initialized, before acquiring the lock. If the variable has not been initialized, a lock is acquired, and a second check is performed to verify that the variable is still uninitialized. If it is still uninitialized, the expensive initialization process is performed, and the shared variable is set to the initialized state. If the variable has already been initialized, the lock is released and the shared variable can be accessed without incurring the overhead of acquiring and releasing the lock.

Double check locking can provide significant performance benefits over simple locking mechanisms, especially in situations where the initialization process is expensive, and the shared variable is accessed frequently by multiple threads. However, it is important to ensure that the implementation is correct, as there are several subtle issues that can arise when using double check locking, such as race conditions and memory consistency problems. Careful consideration of the implementation details, such as the order of memory accesses and the use of memory barriers or synchronization primitives, is required to ensure correct behavior.

### SIngleton class

```cpp
class Singleton {
private:
    static Singleton* instance;
    static std::mutex m;

    Singleton() {}

public:
    static Singleton* getInstance() {
        if (instance == nullptr) {
            std::lock_guard<std::mutex> lock(m);
            if (instance == nullptr) {
                instance = new Singleton();
            }
        }
        return instance;
    }
};

Singleton* Singleton::instance = nullptr;
std::mutex Singleton::m;

```

In this example, the `Singleton` class is defined with a private constructor to prevent external instantiation, and a static `getInstance` method is used to provide access to a single instance of the class.

The `getInstance` method uses double check locking to ensure that only a single instance of the `Singleton` class is created. The first check ensures that the `instance` pointer is not already initialized, and the second check is performed inside a critical section that is guarded by a mutex. If the `instance` pointer is still uninitialized, a new instance of the `Singleton` class is created, and the `instance` pointer is set to the new object.

Note that the `std::mutex` class is used to provide thread-safety for the double check locking mechanism. When a thread enters the critical section, it acquires the mutex lock, preventing other threads from entering the same critical section at the same time. Once the critical section is completed, the lock is automatically released, allowing other threads to enter the critical section if needed.

