# DFS template

Matrix DFS

```python
def dfs_matrix_recursive(matrix, row, col, visited = None):
    if visited is None:
        visited = set()

    rows, cols = len(matrix), len(matrix[0])  
    if (row < 0 or row >= rows 
        or col < 0 or col >= cols 
        or (row, col) in visited):
        return
    
    visited.add((row, col))
    print(matrix[row][col])

    directions = [(-1, 0), (1, 0), (0, -1), (0, 1)]
    for dx, dy in directions:
        new_row, new_col = row + dx, col + dy
        dfs_matrix_recursive(matrix, new_row, new_col, visited)


def dfs_matrix_iterative(matrix, start_row, start_col):
    rows, cols = len(matrix), len( matrix[0])
    visited = set()
    stack = [(start_row, start_col)]
    
    while stack:
        row, col = stack.pop()
        if (row < 0 or row >= rows 
            or col < 0 or col >= cols 
            or (row, col) in visited):
            continue
        
        visited.add((row, col))
        print(matrix[row][col])
        directions = [(-1, 0), (1, 0), (0, -1), (0, 1)]
        for dx, dy in directions:
            new_row, new_col = row + dx, col + dy
            stack.append((new_row, new_col))
```

Tree DFS

```python
class TreeNode:
    def __init__(self, val = 0, left = None, right = None):
        self.val = val
        self.left = left
        self.right = right


def dfs_tree_recursive(root):
    if not root:
        return
    # Preorder traversal
    print(root.val)
    dfs_tree_recursive(root.left)
    dfs_tree_recursive(root.right)


def dfs_tree_iterative(root):
    if not root:
        return
    stack = [root]
    while stack:
        node = stack.pop()
        print(node.val)
        if node.right:
            stack.append(node.left)
        if node.left:
            stack.append(node.right)
```

Graph DFS

```python
def dfs_graph_recursive(graph, start, visited = None):
    if visited is None:
        visited = set()

    visited.add(start)
    print(start)

    for neighbor in graph[start]:
        if neighbor not in visited:
            dfs_graph_recursive(graph, neighbor, visited)

def dfs_graph_iterative(graph, start):
    visited = set()
    stack = [start]

    while stack:
        node = stack.pop()
        if node not in visited:
            visited.add(node)
            print(node)
            stack.extend(neighbor for neighbor in reversed(graph[node])
                         if neighbor not in visited)
```

Backtracking

```python
def backtrack(state):
    if goal_reached(state):
        return
    
    for choice in possible_choices:
        if is_not_valid(choice):
            continue
        apply_choice(state, choice)
        backtrack(state)
        undo_choice(state, choice)
```
