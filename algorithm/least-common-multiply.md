---
description: 'LCM: smallest number divisible by both'
---

# Least Common Multiply

```cpp
#include <iostream>
using namespace std;

// greatest common divisor 最大公约数
long long gcd(long long int a, long long int b) {
    if (b == 0) return a;
    return gcd(b, a % b);
}

// leaset common multiply 最小公倍数
long long lcm(int a, int b) {
    return (a / gcd(a, b)) * b;
}
```
