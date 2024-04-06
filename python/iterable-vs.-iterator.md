# iterable vs. iterator

#### Iterable

1. A object capable of returning members one by one.
2. A iterable can be used in a for loop, must implements `__iter__()`

#### Iterator

1. An object that represents a stream of data
2. A iterator class must override `__next__()`

```python
## Iterate a linked list in for-loop

# Iterator
class NodeIter:
    def __init__(self, node):
        self.curr_node = node
    
    def __next__(self):
        if self.curr_node is None:
            raise StopIteration
        node, self.curr_node = self.curr_node, self.curr_node.next
        return node
        
    # (Optional) iterator must be a iterable, although it is default
    def __iter__(self);
        return self
        
# Iterable
class Node:
    def __init__(self, name):
        self.name = name
        self.next = None
        
    def __iter__(self):
        return NodeIter(self)
        
node1 = Node("1")
node2 = Node("2")
node3 = Node("3")
node1.next = node2
node2.next = node3

for node in node1:
    print(node.name)
    
# Alternatively
it = iter(node1)
first = next(it) # start from second node
for node in it: # it itself is iterable
    print(node.name) 
```

In summary, an iterable is a Python object that can be looped over (i.e., you can iterate over it), while an iterator is an object that keeps state and produces the next value when you call `next()` on it.
