# Python: generator

A generator is a special type of iterator, which is a structure that allows iteration over a sequence of values. It simplifies the creation of iterators using functions rather than classes.&#x20;

## Generators

A **generator** is a function that yields a sequence of results instead of returning a single value. It generates values on the fly using the `yield` statement, which turns the function into a generator. When a generator function is called, it returns a generator object without even beginning execution of the function. The function only executes on next operations.

### yield

The `yield` keyword, when used in a function, allows the function to return an intermediate result to the caller -> remember the point in the function where it left off -> hang on there. When the function is called again, execution resumes from just after the `yield` statement. This behavior makes generators very **memory efficient**, especially for tasks that require iterating over large datasets or complex sequences that you generate on the fly.

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

### Relationship with Iterable and Iterator

* **Generators as Iterators:** Every generator is an iterator. Generators automatically implement the `__iter__()` and `__next__()` methods. Therefore, you can use generators to iterate over data streams efficiently without needing to write class-based iterators.
* **Generators and Iterable Protocol:** Since every generator is an iterator, and since iterators are iterables, generators also fit into the iterable protocol in Python. This means you can use generator objects in any place where an iterable is expected, like loops, list comprehensions, and other iterating constructs.

## Practical usages

#### 1. Return the results as need

```python
RANGE_NUM = 1000
for i in [x*x for x in range(RANGE_NUM)]
    # do something
    print i
    
for i in (x*x for x in range(RANGE_NUM))
    # do something
    print i
```

First method returns a whole list (could be memory inefficient), second method returns a generator. A generator can even be infinite:

```python
def fib():
    a, b = 1, 1
    while True:
        yield a
        a, b = b, a + b
```

#### 2. Generate data stream (e.g. logs)

Assume there is a log file, each line with a record. People can fetch the lines they want by this public API below:

```python
def gen_data_from_file(file_name):
    for line in file(file_name):
        yield line
        
def gen_words(line):
    for word in (w for w in line.split() if w.strip()): # iterate a generator here
        yield word

def count_words(file_name):
    word_map = {}
    for line in gen_data_from_file(file_name):
        for word in gen_words(line):
            if word not in word_map:
                word_map[word] = 0
            word_map[word] += 1
    return word_map
    
def count_total_chars(file_name):
    total = 0
    for line in gen_data_from_file(file_name):
        total += len(line)
    return total
    
if __name__ == '__main__':
    print count_words('test.txt'), count_total_chars('test.txt')
```

Since log file sizes could be very large, using `gen_data_from_file` produces data, and `count_total_chars` consumes data make it memory efficient. Data is only retrieved when needed, not prepared in advance.

#### 3. Asynchronization

> 一些编程场景中，一件事情可能需要执行一部分逻辑，然后等待一段时间、或者等待某个异步的结果、或者等待某个状态，然后继续执行另一部分逻辑。比如微服务架构中，服务A执行了一段逻辑之后，去服务B请求一些数据，然后在服务A上继续执行。或者在游戏编程中，一个技能分成分多段，先执行一部分动作（效果），然后等待一段时间，然后再继续。对于这种需要等待、而又不希望阻塞的情况，我们一般使用回调（callback）的方式。下面举一个简单的例子：

```python
def do(a):
     print('do', a)
     CallBackMgr.callback(5, lambda a = a: post_do(a))
 
 def post_do(a):
     print('post_do', a)
```

这里的CallBackMgr注册了一个5s后的时间，5s之后再调用lambda函数，可见**一段逻辑被分裂到两个函数，而且还需要上下文的传递**（如这里的参数a）。我们用yield来修改一下这个例子，yield返回值代表等待的时间。

```python
@yield_dec
def do(a):
    print('do', a)
    yield 5
    print('post_do', a)
```

这里需要实现一个YieldManager， 通过yield\_dec这个decrator将do这个generator注册到YieldManager，并在5s后调用next方法。Yield版本实现了和回调一样的功能，但是看起来要清晰许多。下面给出一个简单的实现以供参考：

<pre class="language-python"><code class="lang-python"><strong>import sys
</strong># import Timer
import types
import time

class YieldManager(object):
    def __init__(self, tick_delta = 0.01):
        self.generator_dict = {}
        # self._tick_timer = Timer.addRepeatTimer(tick_delta, lambda: self.tick())

    def tick(self):
        cur = time.time()
        for gene, t in self.generator_dict.items():
            if cur >= t:
                self._do_resume_genetator(gene,cur)

    def _do_resume_genetator(self,gene, cur ):
        try:
            self.on_generator_excute(gene, cur)
        except StopIteration,e:
            self.remove_generator(gene)
        except Exception, e:
            print 'unexcepet error', type(e)
            self.remove_generator(gene)

    def add_generator(self, gen, deadline):
        self.generator_dict[gen] = deadline

    def remove_generator(self, gene):
        del self.generator_dict[gene]

    def on_generator_excute(self, gen, cur_time = None):
        t = gen.next()
        cur_time = cur_time or time.time()
        self.add_generator(gen, t + cur_time)

g_yield_mgr = YieldManager()

def yield_dec(func):
    def _inner_func(*args, **kwargs):
        gen = func(*args, **kwargs)
        if type(gen) is types.GeneratorType:
            g_yield_mgr.on_generator_excute(gen)

        return gen
    return _inner_func

@yield_dec
def do(a):
    print 'do', a
    yield 2.5
    print 'post_do', a
    yield 3
    print 'post_do again', a

if __name__ == '__main__':
    do(1)
    for i in range(1, 10):
        print 'simulate a timer, %s seconds passed' % i
        time.sleep(1)
        g_yield_mgr.tick()
</code></pre>

## Reference

{% embed url="https://realpython.com/introduction-to-python-generators/" %}

{% embed url="https://www.cnblogs.com/xybaby/p/6322376.html" %}
