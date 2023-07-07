---
description: What is the difference between Reference and Pointer in C++?
---

# Reference vs. Pointer

In short,

* Reference must be initialized but not allocated memory. Pointer is not initialized when declared, but must be allocated memory when initizalized.
* Reference cannot be changed after initialization. Pointer can change the object it points to.
* Not exists a reference to a `NULL`, but exists a pointer to a `NULL`.

In nature,

* Pointer is a piece of memory space that stores another piece of memory space's address.
* Reference is an alias of certain piece of memory space. It must be initialized because it is an alternative name for an existing variable. It is unchangeable through its whole lifecycle, meaning cannot be unbounded with that variable it refers to.
* Thus, `sizeof(reference)` returns the size of object that reference refers to. `sizeof(pointer)` returns size of pointer itself.

When functions pass parameters,

* Pass a pointer is **pass-by-value**. To pass an address, any operation on this variable is locally.
* Pass a reference is **pass-by-reference**. Any operation on parameters is in a way of indirect addressing.
* Thus, to pass by reference with pointers, we need _1. pass a pointer to the pointer_. Or _2. Referencing and Dereferencing._

So it can say, reference is introduced to simplify pointer and reference is equivalent to `const` pointer in most of cases.

### Reference

[C++ Pointer and References](https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp4\_PointerReference.html)
