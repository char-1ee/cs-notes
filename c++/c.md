---
description: About C
---

# C

### size\_t

`size_t` is the unsigned integer type of the result of [sizeof](https://en.cppreference.com/w/c/language/sizeof) , [\_Alignof](https://en.cppreference.com/w/c/language/\_Alignof) (since C11) and [offsetof](https://en.cppreference.com/w/c/types/offsetof), depending on the [data model](https://en.cppreference.com/w/c/language/arithmetic\_types#Data\_models).

`size_t` can store the maximum size of a theoretically possible object of any type (including array).

`size_t` is commonly used for array indexing and loop counting. Programs that use other types, such as unsigned int, for array indexing may fail on, e.g. 64-bit systems when the index exceeds [UINT\_MAX](https://en.cppreference.com/w/c/types/limits) or if it relies on 32-bit modular arithmetic.

```c
#include <stdio.h>
#include <stddef.h>
#include <stdint.h>
 
int main(void) {
    const size_t N = 100;
    int numbers[N];
    for (size_t ndx = 0; ndx < N; ++ndx)
        numbers[ndx] = ndx;
    printf("SIZE_MAX = %zu\n", SIZE_MAX);
    size_t size = sizeof numbers;
    printf("size = %zu\n", size);
}
```

```
Possible output:
SIZE_MAX = 18446744073709551615
size = 400
```

### ssize\_t and size\_t

```c
ssize_t read(int fd, void* buf, size_t n); // return byte read if success, nor return -1
```

Difference between `ssize_t` _and_ `size_t`, in x86-64 systems, `size_t` is defined as `unsigned long`, while `ssize_t` is defined as `long`. read system call must return a signed value because it must return -1 when encounter error. &#x20;

### void\*

A pointer to `void` is a "generic" pointer type. A `void *` can be converted to any other pointer type without an explicit cast. You cannot dereference a `void *` or do pointer arithmetic with it; you must convert it to a pointer to a complete data type first.

`void *` is often used in places where you need to be able to work with different pointer types in the same code. One commonly cited example is the library function `qsort`:

```c
void qsort(void *base, size_t nmemb, size_t size, 
           int (*compar)(const void *, const void *));
```

`base` is the address of an array, `nmemb` is the number of elements in the array, `size` is the size of each element, and `compar` is a pointer to a function that compares two elements of the array. It gets called like so:

```c
int iArr[10];
double dArr[30];
long lArr[50];
...
qsort(iArr, sizeof iArr/sizeof iArr[0], sizeof iArr[0], compareInt);
qsort(dArr, sizeof dArr/sizeof dArr[0], sizeof dArr[0], compareDouble);
qsort(lArr, sizeof lArr/sizeof lArr[0], sizeof lArr[0], compareLong);
```

The array expressions `iArr`, `dArr`, and `lArr` are implicitly converted from array types to pointer types in the function call, and each is implicitly converted from "pointer to `int`/`double`/`long`" to "pointer to `void`".

The comparison functions would look something like:

```c
int compareInt(const void *lhs, const void *rhs)
{
  const int *x = lhs;  // convert void * to int * by assignment
  const int *y = rhs;

  if (*x > *y) return 1;
  if (*x == *y) return 0;
  return -1;
}
```

By accepting `void *`, `qsort` can work with arrays of any type.

The disadvantage of using `void *` is that you throw type safety out the window and into oncoming traffic. There's nothing to protect you from using the wrong comparison routine:

```c
qsort(dArr, sizeof dArr/sizeof dArr[0], sizeof dArr[0], compareInt);
```

`compareInt` is expecting its arguments to be pointing to `int`s, but is actually working with `double`s. There's no way to catch this problem at compile time; you'll just wind up with a missorted array.
