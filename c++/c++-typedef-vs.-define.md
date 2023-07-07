# typedef vs. #define

`#define` is a preprocessor token: the compiler itself will never see it.\
`typedef` is a compiler token: the preprocessor does not care about it.

`typedef` obeys scoping rules just like variables, whereas `define` stays valid until the end of the compilation unit (or until a matching `undef`).

Most of the time, they have same effect:

```cpp
#define mType int
typedef int mType;
```

But something can be done with `typedef` that cannot be done with `define`:

```cpp
typedef int* int_p1;
int_p1 a, b, c;  // a, b, c are all int pointers

#define int_p2 int*
int_p2 a, b, c; // only the first is a pointer, because int_p2
                 // is replaced with int*, producing: int* a, b, c
                 // which should be read as: int *a, b, c
```

```cpp
typedef int a10[10];
a10 a, b, c;  // create three 10-int arrays

typedef int (*func_p) (int);
func_p fp;  // func_p is a pointer to a function that
            // takes an int and returns an int
```
