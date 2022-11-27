# Sliding window

General template:

```cpp
int left = 0, right = 0;

while (right < s.size()) {
    // ensize window
    window.add(s[right]);
    right++;
    
    while (window needs shrink) {
        // desize window
        window.remove(s[left]);
        left++;
    }
}
```

A more generalized template:

```cpp
/* 滑动窗口算法框架 */
void slidingWindow(string s, string t) {
    unordered_map<char, int> need, window;
    for (char c : t) need[c]++;
    
    int left = 0, right = 0;
    int valid = 0; 
    while (right < s.size()) {
        // c 是将移入窗口的字符
        char c = s[right];
        // 右移窗口
        right++;
        
        // Operate window data

        /*** debug 输出的位置 ***/
        printf("window: [%d, %d)\n", left, right);
        /********************/
        
        // 判断左侧窗口是否要收缩
        while (window needs shrink) {
            // d 是将移出窗口的字符
            char d = s[left];
            // 左移窗口
            left++;
            
            // Operate window data
        }
    }
}
```
