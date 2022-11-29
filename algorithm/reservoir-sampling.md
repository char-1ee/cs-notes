# Reservoir sampling

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

给定一个数据流，数据流长度N很大，且N直到处理完所有数据之前都不可知，请问如何在只遍历一遍数据（O(N)）的情况下，能够随机选取出m个不重复的数据。

1. 如果接收的数据量小于m，则依次放入蓄水池。
2. 当接收到第i个数据时，i >= m，在\[0, i]范围内取以随机数d，若d的落在\[0, m-1]范围内，则用接收到的第i个数据替换蓄水池中的第d个数据。
3. 重复步骤2。

**当处理完所有的数据时，蓄水池中的每个数据都是以m/N的概率获得的。**

{% embed url="https://zhuanlan.zhihu.com/p/119329875" %}

{% embed url="https://zhuanlan.zhihu.com/p/342554800" %}
