---
description: Lazy loading , lazxy initializing, lazy evaluation
---

# Lazy ideas in programming

Some applications of lazy ideas in programming:

* Singleton pattern
* Proxy pattern
* Short-circuit evaluation
* Generator
* Cache
* Dirty flags
* Copy-on-write

### Singleton

```python
class eager_meta(type):
    def __init__(clz, name, bases, dic):
        super(eager_meta, clz).__init__(name, bases, dic)
        clz._instance = clz()

class singleton_eager(object):
    __metaclass__ = eager_meta

    @classmethod
    def instance(clz):
        return clz._instance


class singleton_lazy(object):
    __instance = None
    @classmethod
    def instance(clz):
        if clz.__instance is None:
            clz.__instance = singleton_lazy()
        return clz.__instance
```

### Proxy

代理模式属于责任型模式， 使得一个对象代表另一个对象进行各种操作，常用场景包括

* remote proxy（远程代理），如RMI， RPC
* virtual proxy（虚代理），根据需要创建开销很大的对象，如文档中图片的加载
* （保护代理）：控制对原始对象的访问， 如智能指针

其中 virtual proxy是使用lazy loading很好的例子

### Short-circuit evaluation

Some python function provide short-circuit evaluation features, for example `any`

```python
ret = any(self.calc_and_ret(e) for e in elements)
```

`any(iterable)` function will return True if any element of the iterable is true. If the iterable is empty, return False. For the code above, it will retun immediately when `self.calc_and_ret()` returns true for some element e. Then the elements behind e will not be calculated.

### Generator

```python
for x in [i*i for i in xrange(10000)]:
    # do something
```

So for List Comprehension, Python will allocate memory for all 10000 elements at once. Once the list is created, for for loop starts iterating each element in this list.

```python
for x in (i*i for i in xrange(10000)):
    # do something
```

Forr the generator expression, it generate each element on-the-fly during iteration. Generator does not store all elements in the memory at once. Instead, they yield one value at a time to iterate, which is more memory efficient.

### Cache



## Reference

{% embed url="https://www.cnblogs.com/xybaby/p/6425735.html" %}

{% embed url="https://stackoverflow.com/questions/6760685/what-is-the-best-way-of-implementing-singleton-in-python" %}
