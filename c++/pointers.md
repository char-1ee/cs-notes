# Pointers

Pointers are used extensively in both C and C++ for three main purposes:

* to allocate new objects on the heap,
* to pass functions to other functions
* to iterate over elements in arrays or other data structures.

Raw pointers are discouraged except _where they provide a significant performance benefit and there is no ambiguity as to which pointer is the owning pointer that is responsible for deleting the object_.

Modern C++ provides:

* _smart pointers_ for allocating objects
* _iterators_ for traversing data structures
* _lambda expressions_ for passing functions

### Raw pointers

* An owning pointer (or a copy of it) must be used to explicitly free the heap-allocated object when it's no longer needed. Failure to free the memory results in a _memory leak._ Memory allocated using **`new`** must be freed by using **`delete`** (or **`delete[]`**).
* A **`const`** pointer can't be made to point to a different memory location, and in that sense is similar to a [reference](https://learn.microsoft.com/en-us/cpp/cpp/references-cpp?view=msvc-170).&#x20;
* In general, **`const`** references are the preferred way to pass big objects to functions unless the value of the object can possibly be **`nullptr`**.

### `void*` pointer

* A pointer to **`void`** simply points to a raw memory location. Modern C++ discourages the use of `void` pointers in almost all circumstances.
* When a typed pointer is cast to a `void` pointer, the contents of the memory location are unchanged. However, the type information is lost, so that you can't do increment or decrement operations.&#x20;
* Sometimes it's necessary to use `void*` pointers, for example when passing between C++ code and C functions.

```cpp
void func(void* data, int length) {
    char* c = (char*)(data);
    for (int i = 0; i < length; i++) {
        *c = 0x41;
        ++c;
    }
}
```



### Reference

{% embed url="https://learn.microsoft.com/en-us/cpp/cpp/pointers-cpp?view=msvc-170" %}
