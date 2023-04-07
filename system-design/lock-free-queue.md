# Lock Free Queue

Implement lock-free data structures with atomic operations like Compare & Swap (CAS):

```c
// check whether *reg is old_val, if yes, assign new_val
int compare_and_swap(int* reg, int oldval, int newval) {
    int old_reg_val = *reg;
    if (old_reg_val == oldval) {
        *reg = newval;
    }
    return old_reg_val;
}

// bool version: check whether register upadte successful or not
bool compare_and_swap(int* addr, int oldval, int newval) {
    if (*addr != oldval) {
        return false;
    }
    *addr = newval;
    return true;
}
```

CAS in C++11

{% code overflow="wrap" %}
```cpp
template<class T>
bool atomic_compare_exchange_weak(std::atomic* obj, T* expected, T desired);

template<class T>
bool atomic_compare_exchange_weak(volatile std::atomic* obj, T* expected, T desired);
```
{% endcode %}

Other atomic manipulations are:

* _**Fetch And Add (FAA)**_: atomic +1 operation to variable.
* _**Test-and-set**_: set a value in memory to a specific value (usually non-zero) and returns the previous value. If the previous value was zero, the operation has the effect of acquiring a lock. If the previous value was non-zero, the operation has the effect of indicating that the lock is already held by another thread.
* _**Test and Test-and-set**_: first performs a `test` operation to check if the lock is held by another thread before attempting the `set` operation to acquire the lock. The `test` operation is performed repeatedly until the lock is available, and only then the `set` operation is attempted. This approach is often used to reduce contention on the lock variable and improve performance in highly concurrent systems.

### Linked List Implementation



