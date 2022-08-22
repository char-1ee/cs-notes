---
description: >-
  runtime error: addition of unsigned offset to 0x603000000040 overflowed to
  0x60300000003c (stl_vector.h)
---

# fix: out-of-bound index for std::vector

You set `i` to `0`

```cpp
int i=0;
```

Then you set `k` to `0`.

```cpp
int k=i;
```

Then you use **`k-1`** to index `std::vector<std::string> v`.

```cpp
while(v[k-1].length()
```

`k-1` is `-1`, but it gets worse. `std::vector::operator[]` casts the parameter to an `unsigned` type, producing an **impossibly huge, not-at-all-valid index**.

[No bounds checking is performed with `std::vector::operator[]`](http://en.cppreference.com/w/cpp/container/vector/operator\_at) and you end up treating memory you don't own as if it were a validly constructed `std::string`.
