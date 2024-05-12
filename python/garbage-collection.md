# Garbage collection

## TL;DR

1. python使用引用计数和垃圾回收来释放（free）Python对象.
2. 引用计数的优点是原理简单、将消耗均摊到运行时；缺点是无法处理循环引用.
3. Python垃圾回收用于处理循环引用，但是无法处理循环引用中的对象定义了\_\_del\_\_的情况，而且每次回收会造成一定的卡顿.
4. gc module是python垃圾回收机制的接口模块，可以通过该module启停垃圾回收、调整回收触发的阈值、设置调试选项.
5. 如果没有禁用垃圾回收，那么Python中的内存泄露有两种情况：要么是对象被生命周期更长的对象所引用，比如global作用域对象；要么是循环引用中存在\_\_del\_\_.
6. 使用gc module、objgraph可以定位内存泄露，定位之后，解决很简单.
7. 垃圾回收比较耗时，因此在对性能和内存比较敏感的场景也是无法接受的，如果能解除循环引用，就可以禁用垃圾回收.
8. 使用gc module的DEBUG选项可以很方便的定位循环引用，解除循环引用的办法要么是手动解除，要么是使用weakref.

## References count

Everything in Python is object (mutable and immutable). Each object maintains a counter to indicate how many variables points to this object (`ob_refcnt,` refer to CPython `object.h`).

* When an object is assigned to a variable directly or indirectly, cnt++.
* When `del`variable, or object leaves the code block of variable, cnt--.

```python
# Get ref count of an object, returns actual ref count + 1
sys.getrefcount(a)
```

### Circular reference

```python
1 # -*- coding: utf-8 -*-
 2 import objgraph, sys
 3 class OBJ(object):
 4     pass
 5 
 6 def direct_cycle_reference():
 7     a = OBJ()
 8     a.attr = a
 9     
10 if __name__ == '__main__':
11     direct_cycle_reference()
12     objgraph.show_backrefs(objgraph.by_type('OBJ')[0], max_depth=5, filename = "direct.dot"

```

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

## Garbage collection

Garbage collection here stands for the gc algorithm adopted when there is circular reference. Garbage collection is triggered when:

1. It reaches gc threshold, PVM execute gc automatically.
2. call `gc.collect().`
3. PVM exits.

#### Reachable and collectable

* Reachable: object that can be found starting from root.
* Unreachable: those objects in circular reference are unreachable (gc targets unreachable objects).
* Collectable: unreachable objects can be collected.&#x20;
* Uncollectable: those unreachable objects who have customized `__del__`methods (uncollectable objects causes real memory leak).

#### Algorithm

> 　　在Python中, 所有能够引用其他对象的对象都被称为容器(container). 因此只有容器之间才可能形成循环引用. Python的垃圾回收机制利用了这个特点来寻找需要被释放的对象. 为了记录下所有的容器对象, Python将每一个 容器都链到了一个双向链表中, 之所以使用双向链表是为了方便快速的在容器集合中插入和删除对象. 有了这个 维护了所有容器对象的双向链表以后, Python在垃圾回收时使用如下步骤来寻找需要释放的对象:
>
> 1. 对于每一个容器对象, 设置一个`gc_refs`值, 并将其初始化为该对象的引用计数值.
> 2. 对于每一个容器对象, 找到所有其引用的对象, 将被引用对象的`gc_refs`值减1.
> 3. 执行完步骤2以后所有`gc_refs`值还大于0的对象都被非容器对象引用着, 至少存在一个非循环引用. 因此 不能释放这些对象, 将他们放入另一个集合.
> 4. 在步骤3中不能被释放的对象, 如果他们引用着某个对象, 被引用的对象也是不能被释放的, 因此将这些 对象也放入另一个集合中.
> 5. 此时还剩下的对象都是无法到达的对象. 现在可以释放这些对象了.

> 　　除此之外, Python还将所有对象根据’生存时间’分为3代, 从0到2. 所有新创建的对象都分配为第0代. 当这些对象 经过一次垃圾回收仍然存在则会被放入第1代中. 如果第1代中的对象在一次垃圾回收之后仍然存货则被放入第2代. 对于不同代的对象Python的回收的频率也不一样. 可以通过**`gc.set_threshold(threshold0[, threshold1[, threshold2]])`** 来定义. 当Python的垃圾回收器中新增的对象数量减去删除的对象数量大于threshold0时, Python会对第0代对象 执行一次垃圾回收. 每当第0代被检查的次数超过了threshold1时, 第1代对象就会被执行一次垃圾回收. 同理每当 第1代被检查的次数超过了threshold2时, 第2代对象也会被执行一次垃圾回收.

## Solve memory leak

Memory leak happens when

1. Object is referred by another object has long lifecycle, e.g. ConnectionPool in web server, vairables in global scope.
2. Circular reference object with custom \_\_del\_\_ (because gc cannot collect).

To find memory leak, we can use tools: `objgraph` and `gc`.

### gc module

```python
# Start gc (enabled default)
gc.enable()
gc.disable()
gc.isenabled()

# Do a garbage collection (not matter gc enabled)
gc.collection()

# Set gc threshold
gc.set_threshold(t0, t1, t2)
gc.get_threshold()

# Returns all objects managed by gc collector (time consuming)
gc.get_objects()

# Returns objects that pointed by obj
gc.get_referents(*obj)

# Returns objects that points to obj
gc.get_referrers(*obj)

# Set gc debug flags
# gc.DEBUG_COLLETABLE
# gc.DEBUG_UNCOLLETABLE
# gc.DEBUG_SAVEALL：当设置了这个选项，可以被拉起回收的对象不会被真正销毁（free），
#     而是放到gc.garbage这个列表里面，利于在线上查找问题
gc.set_debug(flags)
```

We can see reference map by getting all objects managed by garbage collector.&#x20;

### objgraph

Based on gc module API, opjgraph provides better APIs.

```python
# Returns the number of objects of certain type
def count(typename)

# Returns object list of certain type
def by_type(typename)

# Print first n=limits many object
def show_most_common_types(limits=10)

# Static the object that increases most since last call
def show_growth()

# Show objects reference map
def show_backrefs()

# Find shortest path to object obj
def find_backref_chain(obj, predicate, max_depth=20, extra_ignore=())

# Draw shortest path to object obj
def show_chain()
```

Practice: find the object with most appearance in a code block.

> 在实际的项目中，我们也不清楚泄露是在哪段代码、哪个模块中发生的，而且往往是发生了内存泄露之后再去排查，这个时候使用obj.show\_most\_common\_types就比较合适了，如果一个自定义的类的实例数目特别多，那么就可能存在内存泄露。如果在压力测试环境，停止压测，调用gc.collet，然后再用obj.show\_most\_common\_types查看，如果对象的数目没有相应的减少，那么肯定就是存在泄露。

## Solve circular reference

If there is circular reference, program must enable gc, otherwise disable gc causes memory leak. On the other hand, gc brings extra cost on performance, for doing one round of gc needs collect all objects managed by garbage collector. Thus, for better performance, disable gc, if disable gc, avoid circular reference.

To solve circular reference, follows 2 steps. First locate circular references, second eliminate them.

### 1. Locate

When locating circular reference, must disable gc first (Also avoid use gc.collect() directly or indirectly).

### 2. Eliminate&#x20;

To eliminate circular reference, we can manually `del` that object before exit. Alternatively, we can use `weakref.`

## Summary

A practice to locate memory leak/circular reference and eliminate them:

Print number of instances/objects of certain class (`show_most_common_types`**,** `show_growth`)&#x20;

\-> Large number of objects indicate possible memory leak or circular reference

For memory leak, `gc.collect()` then `obj.show_most_common_types()` again, if objects number doesnot decrease -> memory leak&#x20;

\-> Detect how memory leak happened with `objgraph.show_backrefs`



For circular reference, check whether the objects are referred by variables from global scope (`objgraph.find_backref_chain(obj)`) -> if no chain returns, no global scope reference.&#x20;

Alternatively, `gc.set_debug()` to print collectable objects.

\-> Manually delete objects after exits their scope, or use `weakref`

## References

{% embed url="https://www.cnblogs.com/xybaby/p/7491656.html" %}

{% embed url="https://instagram-engineering.com/dismissing-python-garbage-collection-at-instagram-4dca40b29172" %}

{% embed url="https://arctrix.com/nas/python/gc/" %}
