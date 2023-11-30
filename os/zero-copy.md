# Zero-copy

#### Choice of zero copy techiques

If want to access and manipulate file content -> mmap()

If just moving data between files -> sendfile(), splice(), as they dont involve user space.

### References

{% embed url="https://zhuanlan.zhihu.com/p/357820303" %}

{% embed url="https://www.toutiao.com/article/6898240850917114380/?wid=1688569894324" %}

{% embed url="https://www.cnblogs.com/xiaolincoding/p/13719610.html" %}

{% embed url="https://juejin.cn/post/6844903949359644680#heading-19" %}
