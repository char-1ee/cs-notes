# Trie

```cpp
const int MAXN = 500005;
int next[MAXN][26], cnt; // 用类似链式前向星的方式存图，next[i][c]表示i号点所连、存储字符为c+'a'的点的编号
void init() // 初始化
{
    memset(next, 0, sizeof(next)); // 全部重置为0，表示当前点没有存储字符
    cnt = 1;
}

// Insert a new string
void insert(const string &s) 
{
    int cur = 1;
    for (auto c : s)
    {
        // 尽可能重用之前的路径，如果做不到则新建节点
        if (!next[cur][c - 'a']) 
            next[cur][c - 'a'] = ++cnt; 
        cur = next[cur][c - 'a']; // 继续向下
    }
}

// Check whether a prefix exists
bool find_prefix(const string &s)
{
    int cur = 1;
    for (auto c : s)
    {
        // 沿着前缀所决定的路径往下走，如果中途发现某个节点不存在，说明前缀不存在
        if (!next[cur][c - 'a'])
            return false;
        cur = next[cur][c - 'a'];
    }
    return true;
}
```

Pseudo code:

```cpp
void insert(String s)
{
    for(every char in string s)
    {
        if(child node belonging to current char is null)
        {
            child node=new Node();
        }
        current_node=child_node;
    }
}

boolean check(String s)
{
    for(every char in String s)
    {
        if(child node is null)    
        {
            return false;
        }
    }
    return true;
}
```
