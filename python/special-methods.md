---
description: Dunder method with dunder score
---

# Special methods

### 1. \_\_new\_\_ vs. \_\_init\_\_

```python
class A:
    # __new___ creates a objet from a class
    def __new__(cls):
        print("__new__")
        return super().__new__(cls)
    
    # After having an object, __init__ initialize this object
    def __init__(self):
        self.x = x
        print("__init__")
        
o = A()
# obj = __new__(A, 1)
# __init__(obj)
```

`__new__` is less frequently used. But can used when you want to customized the creation of an object. For example, creating a Singleton class, which is before creating an object, it checks whether there are other objects already created.

`__new__` must return the class object, `__init__` does not need return.

### 2. \_\_del\_\_

```python
class A:
    # Do something when releasing the object
    def __del__(self):
        print("__del__")
        
o = A()

# __del__ has nothing to do with del, del is just --reference counter to object
del o
```

### 3. \_\_repr\_\_, \_\_str\_\_, \_\_format\_\_

```python
class A:
    # Humane readable text, print() will firstly call __str__()
    def __str__(self):
        return "<A str>"
    
    # More detailed text
    def __repr__(self):
        return "<A repr>"
    
    # Define the format policy
    def __format__(self, spec):
        if spec == "x":
            return "0xA"
        return "<A>"
        
print(f"{A()}")
print(f"{A():x}")
```

Both them return a string representation of an object.&#x20;

### 4. \_\_bytes\_\_

```python
class A:
    # Customize the byte representation of objects
    def __bytes__(self):
        print("__bytes__ called")
        return bytes([0, 1])
        
print(bytes(A()))
```

### 5. \_\_getattr\_\_, \_\_setattr\_\_, \_\_delattr\_\_

```python
class A:
    def __getattr__(self, name):
        print(f"get {name}")
        raise AttributeError
        
    def __getattribute__(self, name):
        if name == "data":
            self.counter += 1    # be cautious about the recursion calls
        return self.__getattribute__(self, name)
        
o = A()
print(o.test)
```

`__getattr__` is called only when you access a non-exitsing attribute in a class.

`__getattribute__` is called when you access any attribue, not matter exists or not.

```python
class A:
    def __init__(self):
        self.data = "abc"
        self.counter = 0
    
    def __setattr__(self, name, val):
        print(f"set {name}")
        super().__setattr__(name, val)
        
class B:
    _attr = {} # class static variable
    def __init__(self):
        self.data = "abc"
        
    def __setattr__(self, name, val):
        self._attr[name] = val
        
    def __getattr__(self, name):
        if name not in self._attr:
            raise AttributeError
        return self._attr[name]
```

`_attr` is a class-level static variable, which means all objects of this call will copy `_attr` when being created.

```python
class A:
    def __init__(self):
        self.data = "abc"
    
    def __delattr__(self, name):
        print(f"del {name}")
        super().__delattr__(name)
        
o = A()
del o.data # delete the 'data' attribute in object o
```

### 6. \_\_dir\_\_

```python
class A:
    def __init__(self):
        self.data = "abc"
    
    # For example, we return all attributes are not built-in
    def __dir__(self):
        lst = super().__dir__()
        return [el for el in lst if not el.startswith("_")]

o = A()
print(dir(o))      
```

`__dir__` returns a sequence, that containes all accessiable methods and variables within an object, including builtin special methods.

### 7. \_\_slots\_\_

```python
class A:
    __solts__ = ['a', 'b']
    
o = A()
o.a, o.b = 0, 1
```

`__slots__` is a whitelist that defines the attributes allowed to customize.
