---
description: 起手式
---

# 起手式

```cpp
auto __FAST_IO__ = []() noexcept {
    std::ios::sync_with_stdio(false);
    std::cin.tie(nullptr);
    std::cout.tie(nullptr); // cout dont need tie actually
}();

// Or

int main() {
    ios::sync_with_stdio(false); // speedup cin, but no longer use scanf
    cin.tie(nullptr);
    // your code
}
```

