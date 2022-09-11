# Kadane's Algorithm

[Kadane's Algorithm](https://en.wikipedia.org/wiki/Maximum\_subarray\_problem#Kadane's\_algorithm) is an algorithm that can find the [maximum sum subarray](https://leetcode.com/problems/maximum-subarray/) given an array of numbers in O(n) time and O(1) space. Its implementation is a very simple example of dynamic programming, and the efficiency of the algorithm allows it to be a powerful tool in some DP algorithms.&#x20;

Kadane's Algorithm involves iterating through the array using an integer variable _**current**_, and at each index _**i**_, determines if elements before index _i_ are "worth" keeping, or if they should be "discarded". The algorithm is only useful when the array can contain negative numbers. If _**current**_ becomes negative, it is reset, and we start considering a new subarray starting at the current index.

Pseudocode

```markdown
// Given an input array of numbers "nums",
1. best = negative infinity
2. current = 0
3. for num in nums:
    3.1. current = Max(current + num, num)
    3.2. best = Max(best, current)

4. return best
```

References

* Leetcode 54. Maximum Sum Subarray
* Leetcode Best time to Buy and Sell Stock
* Leetcode Maximum Sum Circular Subarray
