---
description: from one tiktok interview (a nice interviewer)
---

# RankedDict

### Requirement

Write a dict, map\<string, int>, that implements below 3 functions.

1. set(k, v): insert a key value pair
2. get\_rank(k): given a key, return the rank of its vale (index in a sorted array)
3. get\_key\_from\_rank(rank): given rank, return the key whose value is in has rank

We can use balanced tree like AVL tree, Red-black tree to keep the order while providing logN retreivals.&#x20;

But an intuitive way is to use a map + a multiset/set. `std::map` for the key-value pairs, and a `std::set` or `std::multiset` to maintain keys sorted by their associated values.

```cpp
class RankedDict {
public:
    RankedDict() {}
    
    void set(string k, int v) { // O(logN)
        // if k exists, remove the old rank-key pair
        if (mp.find(k) != mp.end()) {
            st.erase({mp[k], k});
        }
        mp[k] = v;
        st.insert({v, k});
    }
    
    int get_rank(string k) { // O(N)
        if (mp.find(k) == mp.end()) {
            return -1; // key not exists
        }
        // Get rank by counting
        int rank = 0;
        for (auto it = st.begin(); it != st.end(); it++) {
            if (it->second == k) {
                return rank;
            }
            rank++;
        }
        return -1;
    }
    
    string get_key_from_rank(int rank) { // O(1), worst O(n) for iterator nature
        if (rank < 0 || rank >= st.size()) {
            throw out_of_range("Rank out of range");
        }
        auto it = st.begin();
        advance(it, rank); // increase it by rank
        return it->second;
    }
    
private:
    map<string, int> mp; // k, v
    set<pair<int, string>> st; // v, k
}
```

In this approach, we make write operation (set) O(logN), and make read operation O(n).&#x20;

### My method

I used a map  +  a list. The map keeps k-v pairs `map<string , int>`, while the list keeps the sorted v-k pair, `vector<pair<int, string>>`.

* set(k, v): insert a kv pair and then sort the list -> O(nlogn)
* get\_rank(k): iterate lst to find matched key -> O(n)
* get\_key\_from\_rank(rank): return lst\[rank].second -> O(1)

So I came up with further improvements that, we store the rank information together with the kv pair in map, `map<k, pair<v, rank>>`, such taht the get\_rank(k) now is O(1). But the trade-off is set(k, v) now need get the rank information when insert new kv pairs into lst -> extra O(n).

Additionally, I suggest maintain a large list that given (A, 100), we put the coming entry to lst\[100] = k. Therefore, the list would be sparse -> easy to write. Then read would be hard, but we can keep multiple ranges, like \[0, 50], \[50, 100] etc, such that we can concurrently retrieve these ranges to find the targeted value, i.e. range per thread.

### Interviewer hints

You should combine the requirements with a real case, for example, in a game, players register they scores -> set(), and check their rank -> get\_rank(), get\_key\_from\_rank().&#x20;

**Thus, it is important to know it is a read-intensive application or write-intensive application.** it decides we should reduce set() costs or get() costs when trading off.&#x20;

Although the optimal idea is to use a skip list, but algorithm designs should based on real use cases. Skip list idea is similiar to the "range" design I talked above.&#x20;

### Extention

This coding question can be extended:

* Multi-threading: read-write lock
* Big data: map-reduce, concurrency
* etc.

