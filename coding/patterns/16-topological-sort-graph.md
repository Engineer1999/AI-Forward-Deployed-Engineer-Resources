# Pattern 16: Topological Sort (Graph)

## What it is

Given a directed graph of dependencies, topological sort produces a linear order of the nodes such that every edge `u -> v` puts `u` before `v`. It only exists if the graph is a directed acyclic graph (DAG). Kahn's algorithm builds the order by repeatedly removing nodes that currently have no unmet dependencies (in degree 0).

## When to use it

- Tasks, courses, or builds with prerequisites; you need a valid order or to detect a cycle.
- "Can all courses be finished", "build order", "alien dictionary", "recipe with sub recipes".
- Anything phrased as "A must come before B".

## Template (Kahn's algorithm, BFS)

```python
from collections import deque, defaultdict

def topological_order(num_nodes, edges):
    graph = defaultdict(list)
    in_degree = [0] * num_nodes
    for source, dest in edges:          # edge means source must come before dest
        graph[source].append(dest)
        in_degree[dest] += 1

    queue = deque(node for node in range(num_nodes) if in_degree[node] == 0)
    order = []
    while queue:
        node = queue.popleft()
        order.append(node)
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)

    if len(order) != num_nodes:
        return []                       # a cycle exists, no valid order
    return order
```

Time O(V + E), space O(V + E).

## Worked example 1: course schedule

`num_courses` courses labeled `0..n-1`. `prerequisites[i] = [a, b]` means you must take `b` before `a`. Return `True` if you can finish every course.

```python
from collections import deque, defaultdict

def can_finish(num_courses, prerequisites):
    graph = defaultdict(list)
    in_degree = [0] * num_courses
    for course, prereq in prerequisites:
        graph[prereq].append(course)
        in_degree[course] += 1

    queue = deque(c for c in range(num_courses) if in_degree[c] == 0)
    taken = 0
    while queue:
        course = queue.popleft()
        taken += 1
        for nxt in graph[course]:
            in_degree[nxt] -= 1
            if in_degree[nxt] == 0:
                queue.append(nxt)
    return taken == num_courses

# can_finish(2, [[1, 0]]) -> True,  can_finish(2, [[1, 0], [0, 1]]) -> False
```

## Worked example 2: course schedule II (return the order)

```python
from collections import deque, defaultdict

def find_order(num_courses, prerequisites):
    graph = defaultdict(list)
    in_degree = [0] * num_courses
    for course, prereq in prerequisites:
        graph[prereq].append(course)
        in_degree[course] += 1

    queue = deque(c for c in range(num_courses) if in_degree[c] == 0)
    order = []
    while queue:
        course = queue.popleft()
        order.append(course)
        for nxt in graph[course]:
            in_degree[nxt] -= 1
            if in_degree[nxt] == 0:
                queue.append(nxt)
    return order if len(order) == num_courses else []

# find_order(4, [[1, 0], [2, 0], [3, 1], [3, 2]]) -> [0, 1, 2, 3] (one valid order)
```

## Worked example 3: alien dictionary

Given a list of words sorted by the rules of an unknown alphabet, return a possible order of its letters.

```python
from collections import deque, defaultdict

def alien_order(words):
    graph = defaultdict(set)
    in_degree = {ch: 0 for word in words for ch in word}

    for first, second in zip(words, words[1:]):
        for a, b in zip(first, second):
            if a != b:
                if b not in graph[a]:
                    graph[a].add(b)
                    in_degree[b] += 1
                break
        else:
            if len(first) > len(second):
                return ""                # prefix after its longer form, invalid

    queue = deque(ch for ch in in_degree if in_degree[ch] == 0)
    order = []
    while queue:
        ch = queue.popleft()
        order.append(ch)
        for nxt in graph[ch]:
            in_degree[nxt] -= 1
            if in_degree[nxt] == 0:
                queue.append(nxt)

    return "".join(order) if len(order) == len(in_degree) else ""

# alien_order(["wrt", "wrf", "er", "ett", "rftt"]) -> "wertf"
```

Time O(total characters), space O(1) bounded by the alphabet size.

## DFS alternative

Post order DFS also works: visit a node, recurse into its neighbors, then prepend the node to the order. Track three states (unvisited, in progress, done) to detect cycles.

## Common pitfalls

- Reversing the edge direction. Decide whether `[a, b]` means "a before b" or "b before a" and be consistent.
- Not checking `len(order) == num_nodes` to catch cycles.
- Using a set for `graph[a]` matters in the alien dictionary case to avoid double counting an edge in the in degree.

## Practice problems

- Course Schedule
- Course Schedule II
- Alien Dictionary
- Minimum Height Trees
- Sequence Reconstruction
- Parallel Courses
- Find All Possible Recipes from Given Supplies
