# BFS template

Graph BFS - adjacent list

```python
def bfs_graph(graph, start):
    queue = deque([start])
    visited = {start}
    level = 0

    while queue:
        level_size = len(queue)
        for _ in range(level_size):
            node = queue.popleft()
            print(f"visit {node} at {level}")
            for neighbor in graph[node]:
                if neighbor not in visited:
                    visited.add(neighbor)
                    queue.append(neighbor)
        level += 1
    return level
```

```python
class TreeNode:
    def __init__(self, val = 0, left = None, right = None):
        self.val = val
        self.left = left
        self.right = right

def bfs_tree(root):
    if not root:
        return 
    queue = deque([root])
    while queue:
        level_size = len(queue)
        level_nodes = []  # collect nodes for current level
        for _ in range(level_size):
            node = queue.popleft()
            level_nodes.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        print(level_nodes)
```

