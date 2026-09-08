# Pattern 7: Tree Breadth First Search

## What it is

BFS visits a tree level by level, from the root outward, using a queue. You push the root, then repeatedly pop a node, record it, and push its children. Processing the queue one full level at a time gives you per level results, which is what most tree BFS questions want.

## When to use it

- The problem mentions levels, depth, "level order", "zigzag", "right side view", or "minimum depth".
- You want the shortest path in an unweighted tree or graph.
- The answer is naturally grouped by distance from the root.

## Template

```python
from collections import deque

def level_order(root):
    if not root:
        return []
    result = []
    queue = deque([root])
    while queue:
        level_size = len(queue)
        level = []
        for _ in range(level_size):        # process exactly one level
            node = queue.popleft()
            level.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        result.append(level)
    return result
```

The `level_size` snapshot is the trick: it freezes how many nodes belong to the current level before you start adding the next one.

## Worked example 1: zigzag level order

Return the level order traversal but alternate direction: left to right, then right to left, and so on.

```python
from collections import deque

def zigzag_level_order(root):
    if not root:
        return []
    result = []
    queue = deque([root])
    left_to_right = True
    while queue:
        level = deque()
        for _ in range(len(queue)):
            node = queue.popleft()
            if left_to_right:
                level.append(node.val)
            else:
                level.appendleft(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        result.append(list(level))
        left_to_right = not left_to_right
    return result
```

Time O(n), space O(n).

## Worked example 2: minimum depth of a binary tree

The minimum depth is the number of nodes on the shortest path from the root to any leaf. BFS returns as soon as it sees the first leaf.

```python
from collections import deque

def min_depth(root):
    if not root:
        return 0
    queue = deque([(root, 1)])
    while queue:
        node, depth = queue.popleft()
        if not node.left and not node.right:
            return depth
        if node.left:
            queue.append((node.left, depth + 1))
        if node.right:
            queue.append((node.right, depth + 1))

# BFS stops at the shallowest leaf, so it beats DFS on wide, shallow trees
```

Time O(n) worst case, often less, space O(n).

## Worked example 3: right side view

Return the values visible when looking at the tree from the right, top to bottom.

```python
from collections import deque

def right_side_view(root):
    if not root:
        return []
    view = []
    queue = deque([root])
    while queue:
        level_size = len(queue)
        for i in range(level_size):
            node = queue.popleft()
            if i == level_size - 1:        # last node of the level
                view.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
    return view
```

Time O(n), space O(n).

## Common pitfalls

- Reading `len(queue)` inside the inner loop after you have already added children.
- Using a list and `pop(0)`, which is O(n). Use `collections.deque`.
- Forgetting the empty tree case.

## Practice problems

- Binary Tree Level Order Traversal
- Binary Tree Level Order Traversal II
- Binary Tree Zigzag Level Order Traversal
- Average of Levels in Binary Tree
- Minimum Depth of Binary Tree
- Binary Tree Right Side View
- Populating Next Right Pointers in Each Node
- Connect Level Order Siblings
