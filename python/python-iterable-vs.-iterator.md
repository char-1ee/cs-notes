# Python: iterable vs. iterator

### Iterable

1. A object capable of returning members one at a time (e.g. list, tuple, dict, set, string).
2. A iterable can be used in a for loop, and to consider as a iterable, an object must implements `__iter__()` which returns an iterator for that object.

### Iterator

1. An object that represents a stream of data returned one element at a time.&#x20;
2. A iterator object must override 2 methods:&#x20;
   1. `__iter__()`returns the iterator object itself.&#x20;
   2. `__next__()` returns the next item from the stream. When no more data are available, it raises  a \`StopIteration\` exception.

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
