# Decorator

## Function decorator (decorate a function with function)

Python function is also an Python object.

```python
# Return an function
def get_multiple_func(n): 
    def multiple(x):
        return n * x
    return multiple

double = get_multiple_func(2)
triple = get_multiple_func(3)

print(double(5))
print(triple(5))
```

Decorator is also an Python object, because from bytecode level a decorator performs totally same as a Python function.

```python
@dec
def double(x):
    return x * 2

# Totally equivalent to
double = dec(double)
```

Here is an decorator example,&#x20;

```python
import time

def timeit(f):
    def wrapper(*args, **kwargs):
        start = time.time()
        ret = f(*args, **kwargs)
        print(time.time() - start)
        return ret
    
    return wrapper
    
@timeit
def my_func(x):
    time.sleep(x)
        
my_func(1)
```

Notice that decorator allows having arguements:

```python
@timeit(prefix="curr_time: ")
def my_func(x):
    pass
    
# Equivalent to 
my_func = timeit(prefix="curr_time: ")(my_func)
```

Decorator allows nested format:

```python
def timeit(iteration):

    def inner(f):
    
        def wrapper(*args, **kwargs):
            start = time.time()
            ret = f(*args, **kwargs)
            print(time.time() - start)
            return ret
        return wrapper
        
    return inner

@timeit(1000)
def double(x):
    return x * 2

# Totally equivalent to
inner = timeit(1000)
double = inner(double) 
 
# Example use
double(2)      
```

## Decorator  class (decorate a function with class)

```python
class Timer:
    def __init__(self, func):
        self.func = func
    
    def __call__(*args, **kwargs):
        start = time.time()
        ret = self.func(*args, **kwargs)
        print(f"Time: {time.time() - start}")
        return ret
        
@Timer
def add(a, b):
    return a + b
    
# Totally equivalent to
add = Timer(add)

print(add(2, 3))
```

Special method `__call__` makes the class callable. Notice that when we do `add = Timer(add)`, we initialize an object of Timer and name it "add". Then when we call `add(2, 3)`, we are actually call the method `__call__` in this object, which we pass 2 and 3 into `__call__`.

Also, decorator class supports adding arguments:

```python
class Timer:
    def __init__(self, prefix):
        self.prefix = prefix
        
    def __call__(self, func):
        def wrapper(*args, **kwargs):
            start = time.time()
            ret = func(*args, **kwargs)
            print(f"{self.prefix}{time.time() - start}")
            return ret
            
        return wrapper
        
@Timer(prefix="curr_time: ")
def add(a, b):
    return a + b

# Totally equivalent to
add = Timer(prefix="curr_time")(add)
    
print(add(2, 3))
```

## Class decorator (decorate a class with function)

```python
def add_str(cls):
    def __str__(self):
        return str(self.__dict__)
    
    cls.__str__ = __str__
    return cls
    
@add_str
class MyObject:
    def __init__(self, a, b):
        self.a = a
        self.b = b
        
# Totally equivalent to
MyObject = add_str(MyObject) # return a class (not a object)

o = MyObject(1, 2)
print(o) 
# {'a': 1, 'b': 2}
```

## Summary

Decorator is a callable, which lets&#x20;

```python
fn = dec(fn)
cls = dec(cls)
```
