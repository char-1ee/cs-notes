# Return Value Optimization

Return value optimization is a compiler technique. It constructs returned object in the targeted memory space outside of function, so that avoid extra performance costs due to copy returned object.

> The common idea of these two optimizations is to allow the compiler to **use the memory space** of this object t, which is **outside the function**, to directly **construct the object** being initialized **inside** the function and that is returned from it. This effectively removes the need for copying intermediary objects.

```cpp
class BigObject
{
};

BigObject foo() 
{
    return BigObject(); // RVO
}

BigObject bar()
{
   BigObject localObj;
   return localObj; // NRVO
}
```

### RVO (Return Value Optimization)

1. RVO can still apply even when the function has **several return** statements, which could return different objects, as long as the returned objects are created on the return statements.
2. But for the RVO to be applied, the returned object has to be **constructed on a return statement**. Therefore this object does not have a name.

### NRVO (Named Return Value Optimization)

1. NRVO can remove the intermediary objects **even if the returned object has a name** and is therefore not constructed on the return statement.
2.  But, like with the RVO, the function still needs to return a **unique object**, so that the compiler can determine **which object** inside of f it has to construct at the memory location of t (outside of f).



```cpp
T f() {    // only one object, result, can be returned from the function.
    T result(....);
    if (....) {
        return result;
    }
    ....
    return result;
}
```

### Usages

> Note though that compilers have different optimization capabilities, and there is no guarantee that the above optimizations will be applied (although this might be enforced in a future version of the standard for some cases). As a general rule, virtually all compilers apply RVO, and NRVO is applied by most compilers where the function is not too complex (and this varies from compiler to compiler).

Importantly, when **RVO** is not possible the next candidate for optimization is to use **move** semantics. Then, if that fails, we use **copy** operations.

Therefore, to enable compiler optimizations, developers should&#x20;

1. return only one object from all function return paths.
2. have small/coherent functions so that compiler can perform more optimizations.&#x20;

### Reference

{% embed url="https://www.zhihu.com/question/32237405" %}
