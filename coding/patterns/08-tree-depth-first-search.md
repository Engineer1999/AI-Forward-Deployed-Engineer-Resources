# Pattern 8: Tree Depth First Search

## What it is

DFS follows one path from the root as deep as possible before backtracking. It is naturally recursive: solve the problem for a node in terms of its left and right subtrees. Pass state down through parameters and return results up through the call value. An explicit stack replaces recursion when depth could overflow.

## When to use it

- Root to leaf path questions: path sums, counting paths, longest path.
- Subtree properties: height, diameter, balanced check, subtree equality.
- Any tree property defined recursively in terms of children.

## Templates

Top down, carrying state along the path:

```python
def dfs_top_down(node, running_state, results):
    if not node:
        return
    running_state = update(running_state, node)
    if not node.left and not node.right:
        results.append(finalize(running_state))
        return
    dfs_top_down(node.left, running_state, results)
    dfs_top_down(node.right, running_state, results)
```

Bottom up, combining child answers:

```python
def dfs_bottom_up(node):
    if not node:
        return base_case
    left = dfs_bottom_up(node.left)
    right = dfs_bottom_up(node.right)
    return combine(node, left, right)
```

## Worked example 1: path sum exists

Return `True` if some root to leaf path sums to `target`.

```python
def has_path_sum(root, target):
    if not root:
        return False
    if not root.left and not root.right:
        return root.val == target
    remaining = target - root.val
    return has_path_sum(root.left, remaining) or has_path_sum(root.right, remaining)

# tree [5, 4, 8, 11, None, 13, 4, 7, 2], target 22 -> True (5 -> 4 -> 11 -> 2)
```

Time O(n), space O(h) where `h` is the tree height.

## Worked example 2: all root to leaf paths that sum to a target

```python
def path_sum_all(root, target):
    results = []

    def dfs(node, remaining, path):
        if not node:
            return
        path.append(node.val)
        if not node.left and not node.right and remaining == node.val:
            results.append(list(path))
        else:
            dfs(node.left, remaining - node.val, path)
            dfs(node.right, remaining - node.val, path)
        path.pop()                       # backtrack

    dfs(root, target, [])
    return results
```

Time O(n^2) worst case because copying each path is O(n), space O(n).

## Worked example 3: diameter of a binary tree

The diameter is the number of edges on the longest path between any two nodes. It may or may not pass through the root.

```python
def diameter_of_binary_tree(root):
    best = 0

    def height(node):
        nonlocal best
        if not node:
            return 0
        left = height(node.left)
        right = height(node.right)
        best = max(best, left + right)     # path through this node
        return 1 + max(left, right)

    height(root)
    return best

# tree [1, 2, 3, 4, 5] -> 3  (path 4 -> 2 -> 1 -> 3)
```

Time O(n), space O(h). The trick is returning height while updating the best diameter as a side effect.

## Iterative DFS

```python
def preorder_iterative(root):
    if not root:
        return []
    result, stack = [], [root]
    while stack:
        node = stack.pop()
        result.append(node.val)
        if node.right:
            stack.append(node.right)     # push right first so left is processed first
        if node.left:
            stack.append(node.left)
    return result
```

## Common pitfalls

- Forgetting to backtrack (`path.pop()`) after exploring both children.
- Treating a node with one child as a leaf. A leaf has no children at all.
- Deep recursion on a skewed tree. Convert to an explicit stack if `n` can be large.

## Practice problems

- Path Sum
- Path Sum II
- Path Sum III
- Sum Root to Leaf Numbers
- Binary Tree Maximum Path Sum
- Diameter of Binary Tree
- Balanced Binary Tree
- Lowest Common Ancestor of a Binary Tree
- Count Good Nodes in Binary Tree
