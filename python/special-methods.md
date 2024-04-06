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
