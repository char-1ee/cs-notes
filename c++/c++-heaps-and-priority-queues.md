# Heaps and Priority Queues

### Implementation

A heap is stored as std::vector, this representation is more efficient than having nodes pointing to each other for several reasons:

* there is only one dynamic allocation for all the heap, and not one per node,
* there is no pointers to children, so no space needed for them,
* the contiguous layout of the structure makes it more cache-friendly.

Parent node jumps to children nodes (start from index 1), _index \* 2_ and _index \* 2 + 1._ Start from 0, left node _index \* 2  - 1,_ right node _index \* 2_

### Heap

#### make\_heap

<pre class="language-cpp"><code class="lang-cpp"><strong>// Make a range of comparables into max heap
</strong><strong>std::vector&#x3C;int> numbers = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
</strong>std::make_heap(numbers.begin(), numbers.end()); 

// Check whether it is structured as a max heap implemented as an array
std::is_heap(begin(numbers), end(numbers));

// It is possible that only the beginning of a collection is structured as 
// a heap. Returns the iterator pointing to the first position of the 
// collection that does not respect the heap property.
auto heapUntil = std::is_heap_until(begin(numbers), end(numbers));
</code></pre>

#### Push\_heap, pop\_heap, sort\_heap

```cpp
// Push new element into heap, need push to vector first
myHeap.push_back(8.8);
std::push_heap(begin(myHeap), end(myHeap));

// pop_heap will swap the top element to the end of vector
std::pop_heap(begin(myHeap), end(myHeap));
myHeap.pop_back()

// Convert heap into sorted vector
std::sort_heap(begin(myHeap), end(myHeap));
```

### Priority Queue

```cpp
template<typename T>
void print_queue(T q) { // NB: pass by value so the print uses a copy
    while(!q.empty()) {
        std::cout << q.top() << ' ';
        q.pop();
    }
    std::cout << '\n';
}
 
int main() {
    std::priority_queue<int> q;
 
    const auto data = {1,8,5,6,3,4,0,9,7,2};
 
    for(int n : data)
        q.push(n);
 
    print_queue(q);
 
    std::priority_queue<int, std::vector<int>, std::greater<int>>
        q2(data.begin(), data.end());
 
    print_queue(q2);
 
    // Using lambda to compare elements.
    auto cmp = [](int left, int right) { return (left ^ 1) < (right ^ 1); };
    std::priority_queue<int, std::vector<int>, decltype(cmp)> q3(cmp);
 
    for(int n : data)
        q3.push(n);
 
    print_queue(q3);
}
```

### Relationships

Notice that heap is ont of the implementations of priority queue. But using heap can provide more usibility for example,&#x20;

```cpp
size_t leftChild(size_t index) { 
    return (index + 1) * 2 - 1; 
} 

size_t rightChild(size_t index) { 
    return (index + 1) * 2; 
}
```

to access any element in heap, while priority queue can only provide top() method.



