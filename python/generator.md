# Generator

A generator is a special type of iterator, which is a structure that allows iteration over a sequence of values.&#x20;

### yield

The `yield` keyword, when used in a function, allows the function to return an intermediate result to the caller and remember the point in the function where it left off. When the function is called again, execution resumes from just after the `yield` statement. This behavior makes generators very memory efficient, especially for tasks that require iterating over large datasets or complex sequences that you generate on the fly.

### Example

* A function with `yield` is a generator function
* Call a generator function will return a generator object
* Only call `next()` to a generator object can return values

```python
def gen(num):
    while num > 0:
        yield num
        num -= 1
    return
    
g = gen(5)

first = next(g) # return 5

for i in g: # g is iterable
    print(i) # 5, 4, 3, 2, 1
```

### Reference

{% embed url="https://realpython.com/introduction-to-python-generators/" %}
