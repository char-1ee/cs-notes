# Monotonic Stack

A monotonic stack is a stack whose elements are monotonically increasing or descreasing.

Sometimes we store the index of the elements in the stack and make sure the elements corresponding to those indexes in the stack forms a mono-sequence.

Monotonic stack is mainly to solve **NGE** probelms (Next Greater Element), which says to each element in array, monostack helps to find the next element that greater than it.

```cpp
#include <bits/stdc++.h>
using namespace std;
int main() {
    ios::sync_with_stdio(false);
    int n;
    cin >> n;
    vector<int> V(n + 1), ans(n + 1);
    for (int i = 1; i <= n; ++i)
        cin >> V[i];
    stack<int> S;
    for (int i = 1; i <= n; ++i) {
        while (!S.empty() && V[s.top()] < V[i]) {
            ans[s.top()] = i;
            S.pop();
        }
        S.push(i);
    }
    for (int i = 1; i <= n; ++i)
        cout << ans[i] << " ";
    return 0;
}
```

or find the first smaller element than it when iterating array from left. Say we have \[2, 1, 4, 6, 5], there are no greater elements to the left for elements 2, 1. But for 4, 1 is the first smaller element in its left, and same for 6. When it comes to 5, `S.top() == 6 > 5` so pop 6 and the we find 4 is the first smaller element to 5.&#x20;

Similarly above applys to monotonic decreasing stacks.

### Insights

How to identify a monotonic stack problem?

1. Increasing/decreasing trend. There is increasing trend, push into the stack. When there is an elements breaks the trend, pop from the stack.&#x20;
2. Find next/previous greater/smaller in a list with a _linear time_ constraint. &#x20;

### References

* [Zhihu - Monotonic stack](https://www.zhihu.com/search?type=content\&q=%E5%8D%95%E8%B0%83%E6%A0%88)
* [LeetCode Monotonic Stack Summary](https://www.cnblogs.com/grandyang/p/8887985.html)
* [Gitbook - Monotonic Stack](https://liuzhenglaichn.gitbook.io/algorithm/monotonic-stack)
* [LeetCode - Monotonic Stack Discuss](https://leetcode.com/tag/monotonic-stack/discuss/2347639/A-comprehensive-guide-and-template-for-monotonic-stack-based-problems)

### Examples

* [LeetCode collections](https://leetcode.com/tag/monotonic-stack/)
* [LeetCode 42. Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)
* [LeetCode 84. Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/)
