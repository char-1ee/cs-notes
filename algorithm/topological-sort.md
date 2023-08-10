# Topological Sort

Topological sort is to sort the nodes on a DAG, such that every directed edge u -> v, u appears before v. In short, transforming a DAG into a list without breaking out order of nodes.&#x20;

### Kahn's algorithm

Time: O(m + n)

```cpp
// deg是入度，在存图的时候需要录入数据
// A是排序后的数组
int deg[MAXN], A[MAXN], edges[MAXN];
bool toposort(int n) {
    int idx = 0;
    queue<int> q;
    for (int i = 1; i <= n; i++) {
        if (deg[i] == 0) {
            q.push(i);
        }
    }
    
    while (!q.empty()) {
        int t = q.front();
        q.pop();
        A[idx++] = t;
        for (auto to : edges[t]) {
            deg[to]--;
            if (deg[to] == 0) {
                q.push(to);
            }
        }
    }
    return idx == n;
}
```

Returns whether a topological sort succeed / whether exists a circle in graph. Thus, topo soty can be used to check circle.&#x20;

Sometimes there needs to output the DAG in lexicographically smallest order, then we can change \`queue\` into \`priority\_queue\`.

### Cases

* [https://leetcode.com/problems/course-schedule/description/](https://leetcode.com/problems/course-schedule/description/)
* [https://leetcode.com/problems/course-schedule-ii/description/?envType=study-plan-v2\&envId=tiktok-spring-23-high-frequency](https://leetcode.com/problems/course-schedule-ii/description/?envType=study-plan-v2\&envId=tiktok-spring-23-high-frequency)

### Reference

{% embed url="https://zhuanlan.zhihu.com/p/260112913" %}
