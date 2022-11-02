# Sieve of Eratosthenes

Given a number n, find out all primes smaller than or equal to n.&#x20;

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int n;
    cin >> n;
    bool prime[n + 1];
    memset(prime, true, sizeof(prime));
    
    for (int i = 2; i * i <= n; i++) {
        if (prime[i] == true) {
            for (int j = i * i; j <= n; j++) {
                prime[j] = false;
            }
        }
    }
    for (int i = 2; i <= n; i++) {
        if (prime[i])
            cout << i << " ";
    }
}
```
