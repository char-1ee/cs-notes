---
description: LCS and DP
---

# Longest Common Subsequence

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Recursion with memoization

<pre class="language-cpp"><code class="lang-cpp">#include &#x3C;algorithm>
#include &#x3C;cstring>
#include &#x3C;iostream>
using namespace std;

const int MAXN = 505;
char s1[MAXN], s2[MAXN];
int dp[MAXN][MAXN];

int lcs(int n1, int n2)
<strong>{
</strong>    if (dp[n1][n2] != -1)
        return dp[n1][n2];
    if (n1 == 0 || n2 == 0)
        dp[n1][n2] = 0;
    else if (s1[n1 - 1] == s2[n2 - 1])
        dp[n1][n2] = lcs(n1 - 1, n2 - 1) + 1;
    else
        dp[n1][n2] = max(lcs(n1, n2 - 1), lcs(n1 - 1, n2));
    return dp[n1][n2];
}
int main()
{
    while (cin >> s1 >> s2)
    {
        memset(dp, -1, sizeof(dp));
        cout &#x3C;&#x3C; lcs(strlen(s1), strlen(s2)) &#x3C;&#x3C; endl;
    }
    return 0;
}</code></pre>

Or iteration

```cpp
while (cin >> s1 >> s2)
{
    memset(dp, 0, sizeof(dp));
    int n1 = strlen(s1), n2 = strlen(s2);
    for (int i = 1; i <= n1; ++i)
        for (int j = 1; j <= n2; ++j)
            if (s1[i - 1] == s2[j - 1])
                dp[i][j] = dp[i - 1][j - 1] + 1;
            else
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
    cout << dp[n1][n2] << endl;
}
```

Output

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption><p>Given "abecbab" and "bdcbabb"</p></figcaption></figure>

### Reference

* [Zhihu - 算法学习笔记(58): 最长公共子序列](https://zhuanlan.zhihu.com/p/311598413)
* [Zhihu - 最长公共子序列、最长公共子串问题](https://zhuanlan.zhihu.com/p/47597440)
