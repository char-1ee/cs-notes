---
description: C++ implementation
---

# LRU cache

LRUCache: data + index. Using a linked list to maintain data (O(1)) and the time status of data. Using a hashmap for data operations.

Define a double linked list node&#x20;

```cpp
class Node {
public:
    Node(int k, int v): key(v), val(v) {
        next = prev = nullptr;
    }
    
    int key;
    int val;
    Node* prev;
    Node* next;
};
```

Helper member functions

```cpp
class LRUCache {
private:
    // Bare pointer memory management
    void release(Node* head) {
        // release memory
    }
    
    // Detach a node from ll and return
    Node* detach(Node* node) {
        node->prev->next = node->next;
        node->next->prev = node->prev;
        return node;
    }
    
    // Attach new node to head of ll
    Node* attach(Node* head, Node* node) {
        node->next = head->next;
        node->prev = head;     
        head->next->prev = node;
        head->next = node;
    }
    
    int cap_; // capacity of cache
    Node* head_;
    Node* tail_;
    std::unordered_map<int, Node*> index_; // key -> node
};
```

Main body

```cpp
class LRUCache {
public:
    LRUCache(int capacity): cap_(capacity) {
        head_ = new Node(-1, -1);
        tail_ = new Node(-1, -1);
        head_->next = tail_;
        tail_->prev = head_;
    }
    
    ~LRUCache() {
        release(head_);
    }
    
    int get(int key) {
        auto it = index_.find(key);
        if (it == index_.end()) {
            return -1;
        }
        
        // If found then put it to head of ll
        auto* node = it->next;
        attach(head_, detach(node))
        return node->val;
    }
    
    void put(int key, int value) {
        auto it = index_.find(key);
        
        // If found, update value and put to head
        if (it != index_.end()) {
            it->second->val = value;
            attach(head_, detach(it->second));
        }
           
        // If not found, add the node and put to head
        Node* node = new Node(key, value);
        index_[key] = attach(head_, node);
        
        // Check capacity, if exceeds, delete oldest node
        if (index_.size() > cap_) {
            auto node_to_del = detach(tail_->prev);
            index_.erase(ndoe_to_del->key);
            delete node_to_del;
        }
    }
};
```
