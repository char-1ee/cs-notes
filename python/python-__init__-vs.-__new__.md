# \_\_init\_\_ vs. \_\_new\_\_

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def __new__(cls, name, age):
        return super(Person, cls).__new__(cls, name, age)
```

\_\_new\_\_ is called before \_\_init\_\_, the process of instaniating Person class be lie:

1. p = Person(name, age)
2. Call \_\_new\_\_ method, it returns an instance of Person class.
3. The returned instance call \_\_init\_\_ method, to intialize some values.

\_\_init\_\_ is for initialization -> instance-level method.

\_\_new\_\_ is for instantiation -> class-level method.

### \_\_new\_\_

1. Used when you inherit immutable class (int, str, tuple), by providing a method to customize instantiation of your class.&#x20;

```python
class PositiveInteger(int):
    def __new__(cls, val):
        return super(PositiveInteger, cls).__new__(cls, abs(val))
```

2. Used for your metaclass.
3. Used for Singleton pattern.

```python
class Singleton:
    def __new__(cls):
        if not hasattr(cls, 'instance'):
            cls.instance = super(Singleton, cls).__new__(cls)
        return cls.instance
```
