---
description: How to make your python code pythonic
---

# Use less for-loop in Python

### List comprehension

```python
from timeit import timeit

def with_for():
    lst = []
    for i in range(100):
        lst.append(i) # time consuming
    return lst
    
def without_for_1():
    return [i for i in range(100)] # faster
    
def without_for_2():
    return (i for i in range(100)) # even more faster
    
print(f"with     {timeit(with_for, number=10000)}")
print(f"without: {timeit(without_for_1, number=10000)}")
```

For the function `without_for_2`, it actually returns a generator (generator comprehension). Because generator does not actually build a list, it is even more faster than list comprehension.

#### Filter

```python
def with_for():
    ret = []
    for num in lst:
        if valid(num):
            ret.append(num)
    return ret
    
def without_for():
    return [num for num in lst if valid(num)]
    
# Alternatively, we can use built-in methods
def without_for_built_in():
    return filter(valid, lst) # return a generator
```

#### Map

```python
def change():
    return num * 2

def with_for():
    ret = []
    for num in lst:
        ret.append(change(num))
    return ret
    
def without_for():
    # return (change(num) for num in lst)
    return map(change, lst)
```

### Max, Min

```python
lst = [i for i in range(1000)]

max(lst)
min(lst)
```

Built-in methods are optimal from bytecode level, compared to users' custom implementations. Thus, it is advised to use built-in methods as can as possible.

### Any, All

```python
lst = [i for i in range(1000)]

def with_for():
    for num in lst:
        if num > 50:
            return True
    return False
    
def without_for():
    return any(num > 50 for num in lst) # generator
```

When you pass generator as an arguement into function call, then it does not need generator's outter brackets.

```python
lst = [i for i in range(1000)]

def with_for():
    for num in lst:
        if num > 100:
            return False
    return True
    
def without_for():
    return all(num > 100 for num in lst) 
```

### Zip

<pre class="language-python"><code class="lang-python">lst = [i for i in range(1000)]
lst2 = [i for i in range(1000)]

def with_for():
<strong>    ret = []    
</strong>    for i in range(len(lst)):
        ret.append((lst[i], lst2[i]))
    return ret
    
def without_for():
    return zip(lst, lst2) # return a tuple 
</code></pre>

A better example

```python
names = ["Alice", "Bob", "Char"]
ages = [18, 16, 12]
scores = [3, 4, 5]

def with_for():
    for i in range(len(names)):
        print(names[i], ages[i], scores[i])

def without_for():
    for name, age, score in zip(names, ages, scores):
        print(name, age, score)
```
