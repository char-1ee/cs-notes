# \_\_slots\_\_

The special attribute `__slots__` allows you to explicitly state which instance attributes you expect your object instances to have, with expected results:

1. **Faster** attribute access
2. **Space savings** in memory&#x20;
3. Imporve code **robustness**&#x20;

```python
class A:
    __slots__ = ("f", "g") # whitelist attributes

class B:
    pass

o = A()
o.b = 2 # error
o.f = 2 # good

o = A()
p = B()
o.f = 2
p.f = 2

print(p.__dict__) # {'f': 2}
print(o.__dict__) # 'A' object has no attribute '__dict__'
```

There is no `__dict__` in object o because attributes of A is stored in advanced allocated space of `__slots__`.

Consider the usage of `__slots__` in class inheritance, the best practice is,

> Factor out all but one or all parents' abstraction which their concrete class respectively and your new concrete class collectively will inherit from - giving the abstraction(s) empty slots (just like abstract base classes in the standard library).

which is,

* Define general, abstract parent classes without specifying `__slots__`, keeping them flexible and focused on defining a broad interface.
* For your specific, concrete classes that inherit from these abstractions, consider defining `__slots__` to optimize memory usage by explicitly stating which attributes those concrete classes will have.

### Reference&#x20;

{% embed url="https://stackoverflow.com/questions/472000/usage-of-slots" %}
