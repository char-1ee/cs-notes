# Constraints

一般ACM或者笔试题的时间限制是1秒或2秒。 在这种情况下，C++代码中的操作次数控制在 `10^7∼10^8` 为最佳。

下面给出在不同数据范围下，代码的时间复杂度和算法该如何选择：

1. n≤30, 指数级别, dfs+剪枝，状态压缩dp
2. n≤100 => O(n^3)，floyd，dp，高斯消元&#x20;
3. n≤1000 => O(n^2)，O(n^2 x logn)，dp，二分，朴素版Dijkstra、朴素版Prim、Bellman-Ford&#x20;
4. n≤10000 => O(n∗√n)，块状链表、分块、莫队
5. n≤100000 => O(nlogn) => 各种sort，线段树、树状数组、set/map、heap、拓扑排序、dijkstra+heap、prim+heap、Kruskal、spfa、求凸包、求半平面交、二分、CDQ分治、整体二分、后缀数组、树链剖分、动态树&#x20;
6. n≤1000000 => O(n), 以及常数较小的 O(nlogn) 算法 => 单调队列、 hash、双指针扫描、并查集，kmp、AC自动机，常数比较小的 O(nlogn) 的做法：sort、树状数组、heap、dijkstra、spfa
7. n≤10000000 => O(n)，双指针扫描、kmp、AC自动机、线性筛素数&#x20;
8. n≤109 => O(√n)，判断质数
9. n≤10^18 => O(logn)，最大公约数，快速幂，数位DP&#x20;
10. n≤10^1000 => O((logn)^2)，高精度加减乘除&#x20;
11. n≤10^100000 => O(logk×loglogk)，k表示位数，k表示位数，高精度加减、FFT/NTT
