# Brian Kernighan Algorithm

### Description

Given any integer x,  f(x) = x & (x - 1).  f(x) is the result for x to unset its rightmost set bit.

![example](<../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png>)

Similarly, x & (\~x + 1) fetchs out the rightmost set bit.

```cpp
void lowbit(int x) { x &= -x; }
```

### Examples

* [Leetcode 461. Hamming Distance](https://leetcode.com/problems/hamming-distance/)
* [Leetcode 201. Bitwise AND of Numbers Range](https://leetcode.com/problems/bitwise-and-of-numbers-range/)
* [Leetcode 231. Power of Two](https://leetcode.com/problems/power-of-two/)
* [Number of set bit from 0 to n](https://leetcode.cn/problems/w3tCBm/)
