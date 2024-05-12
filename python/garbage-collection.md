# Garbage collection

## TL;DR

1. python使用引用计数和垃圾回收来释放（free）Python对象
2. 引用计数的优点是原理简单、将消耗均摊到运行时；缺点是无法处理循环引用
3. Python垃圾回收用于处理循环引用，但是无法处理循环引用中的对象定义了\_\_del\_\_的情况，而且每次回收会造成一定的卡顿
4. gc module是python垃圾回收机制的接口模块，可以通过该module启停垃圾回收、调整回收触发的阈值、设置调试选项
5. 如果没有禁用垃圾回收，那么Python中的内存泄露有两种情况：要么是对象被生命周期更长的对象所引用，比如global作用域对象；要么是循环引用中存在\_\_del\_\_
6. 使用gc module、objgraph可以定位内存泄露，定位之后，解决很简单
7. 垃圾回收比较耗时，因此在对性能和内存比较敏感的场景也是无法接受的，如果能解除循环引用，就可以禁用垃圾回收
8. 使用gc module的DEBUG选项可以很方便的定位循环引用，解除循环引用的办法要么是手动解除，要么是使用weakref

## References count







### References

{% embed url="https://www.cnblogs.com/xybaby/p/7491656.html" %}

{% embed url="https://arctrix.com/nas/python/gc/" %}
