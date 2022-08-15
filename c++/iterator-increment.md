---
description: Why I can't use `+1` in the iterator of `map` in c++?
---

# Iterator increment

```cpp
list<int>::iterator it = curr++;
if (it == list.end()) {
    it = list.begin();
}
--curr;
```

`std::vector::iterator` is `LegacyRandomAccessIterator`, it supports operations like `it += k`.

`std::map::iterator` and `std::list::iterator` is `LegacyBidirectionIterator`, it only supports operation `it++`, `it--`&#x20;
