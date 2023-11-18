---
description: From OS perspective
---

# OS-level program performance optimization

### **Locality**

* **时间局部性**。如果一个数据在某个时间点被CPU访问了，那么在接下来很短的一段时间内，这个数据很有可能会再次被CPU访问到。
* **空间局部性**。如果一个数据在某个时间点被CPU访问了，那么与这个数据临近的其他数据，很有可能也会很快被CPU访问到

## TMP: Cache Line

When the processor accesses a part of memory that is not already in the cache it loads a chunk of the memory around the accessed address into the cache, hoping that it will soon be used again. The chunks of memory handled by the cache are called cache lines.

Different CPU has difference size of cache lines, classic one is 64 bytes.

## TMP: Cache False Sharing



## Unroll Loop&#x20;

#### Baseline

```c
// test.c
// Avoid GCC makes calc() inlined in main()
__attribute__((noinline))
int calc(int n) {
    int fact = 1;
    for (int i = 1; i < n; i++) {
        fact *= i;
    }
    return fact;
}

int main() {
    return calc(1000000);
}
```

<pre class="language-sh"><code class="lang-sh"><strong>gcc test.c -o test
</strong><strong>time ./test
</strong># real    0m3.201s
# user    0m3.197s
# sys     0m0.005s
</code></pre>

#### O3 optimization

```bash
gcc test.c -o test -O3
time ./test
# real    0m1.070s
# user    0m1.069s
# sys     0m0.000s
```

#### Unroll loop&#x20;

```c
// test2.c
// Avoid GCC makes calc() inlined in main()
__attribute__((noinline))
int calc(int n) {
    int fact0 = 1, fact1 = 1, fact2 = 1, fact3 = 1;
    
    // If (n%4 != 0), then deal with last (n%4) numbers outside loop
    for (int i = 1; i < n; i += 4) {
        fact0 *= i;
        fact1 *= i + 1;
        fact2 *= i + 2;
        fact3 *= i + 3;
    }
    return fact0 * fact1 * fact2 * fact3;
}

int main() {
    return calc(1000000);
}
```

<pre class="language-bash"><code class="lang-bash">gcc test2.c -o test2
time ./test2.c
<strong># real    0m1.051s
</strong># user    0m1.046s
# sys     0m0.005s
</code></pre>

#### Unroll loop in a wrong way

```c
// test3.c
// Avoid GCC makes calc() inlined in main()
__attribute__((noinline))
int calc(int n) {
    int fact = 1;
    
    // If (n%4 != 0), then deal with last (n%4) numbers outside loop
    for (int i = 1; i < n; i += 4) {
        fact *= i;
        fact *= i + 1;
        fact *= i + 2;
        fact *= i + 3;
    }
    return fact;
}

int main() {
    return calc(1000000);
}
```

<table><thead><tr><th width="103"></th><th width="123">test.c</th><th width="141">test2.c (unroll)</th><th>test3.c (false unroll)</th><th>test2.c (register)</th></tr></thead><tbody><tr><td>-O0</td><td>3.2</td><td>1.05</td><td>3.07</td><td>0.36</td></tr><tr><td>-O3</td><td>1.07</td><td>0.37</td><td>0.81</td><td>NA</td></tr></tbody></table>

#### Optimize memory access on program hot path

```c
// test2_hot_apth.c
__attribute__((noinline))
int calc(int n) {

    // `register` keyword advice the compiler to put variables into registers  
    // as can as possible, to speed up the access to these variables.
    register fact0 = 1, fact1 = 1, fact2 = 1, fact3 = 1;
    
    for (register int i = 1; i < n; i += 4) {
        fact0 *= i;
        fact1 *= i + 1;
        fact2 *= i + 2;
        fact3 *= i + 3;
    }
    return fact0 * fact1 * fact2 * fact3;
}

int main() {
    return calc(1000000);
}
```

## Reference

{% embed url="https://mp.weixin.qq.com/s/Mm8PqPM1vULK9Yr8tOnOgg" %}
Cache line
{% endembed %}

{% embed url="https://mp.weixin.qq.com/s/JpwrE2LRYzBSzIaFl2TY9g" %}
Cache False Sharing
{% endembed %}

{% embed url="https://mp.weixin.qq.com/s/LCvWnAdM4wUiGTqSoiBxrA" %}
Unroll loop
{% endembed %}
