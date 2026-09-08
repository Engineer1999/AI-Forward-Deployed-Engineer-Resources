# Pattern 14: K-way Merge

## What it is

You have `K` sorted lists and need to process their combined order without concatenating and re sorting. A min heap holds one candidate from each list, the current smallest across all fronts. Pop the smallest, output it, then push the next element from the list it came from. Each of the `n` total elements passes through the heap once, so the cost is O(n log k).

## When to use it

- Merge `K` sorted linked lists or arrays.
- Find the `K`th smallest element across multiple sorted sequences.
- The smallest range that includes at least one number from each of `K` lists.
- A sorted matrix, where each row or column is a sorted list.

## Template

```python
import heapq

def merge_k_sorted(lists):
    heap = []
    for list_index, seq in enumerate(lists):
        if seq:
            heapq.heappush(heap, (seq[0], list_index, 0))   # (value, which list, index in list)

    merged = []
    while heap:
        value, list_index, element_index = heapq.heappop(heap)
        merged.append(value)
        next_index = element_index + 1
        if next_index < len(lists[list_index]):
            heapq.heappush(heap, (lists[list_index][next_index], list_index, next_index))
    return merged

# merge_k_sorted([[1, 4, 5], [1, 3, 4], [2, 6]]) -> [1, 1, 2, 3, 4, 4, 5, 6]
```

The tuple carries the list index so you know where to pull the next element from. Including `element_index` keeps the tuples comparable even when values tie.

## Worked example 1: merge K sorted linked lists

```python
import heapq

def merge_k_lists(lists):
    heap = []
    for i, node in enumerate(lists):
        if node:
            heapq.heappush(heap, (node.val, i, node))

    dummy = tail = ListNode(0)
    while heap:
        value, i, node = heapq.heappop(heap)
        tail.next = node
        tail = node
        if node.next:
            heapq.heappush(heap, (node.next.val, i, node.next))
    return dummy.next
```

Time O(n log k), space O(k).

## Worked example 2: Kth smallest element in a sorted matrix

Each row and each column of `matrix` is sorted in ascending order. Return the `K`th smallest value.

```python
import heapq

def kth_smallest(matrix, k):
    n = len(matrix)
    heap = [(matrix[r][0], r, 0) for r in range(min(k, n))]
    heapq.heapify(heap)

    value = None
    for _ in range(k):
        value, r, c = heapq.heappop(heap)
        if c + 1 < n:
            heapq.heappush(heap, (matrix[r][c + 1], r, c + 1))
    return value

# kth_smallest([[1, 5, 9], [10, 11, 13], [12, 13, 15]], 8) -> 13
```

Time O(k log(min(k, n))), space O(min(k, n)).

## Worked example 3: smallest range covering elements from K lists

Find the smallest range `[a, b]` such that each of the `K` sorted lists has at least one number in `[a, b]`.

```python
import heapq

def smallest_range(lists):
    heap = [(seq[0], i, 0) for i, seq in enumerate(lists)]
    heapq.heapify(heap)
    current_max = max(seq[0] for seq in lists)
    best = [heap[0][0], current_max]

    while True:
        value, i, j = heapq.heappop(heap)
        if current_max - value < best[1] - best[0]:
            best = [value, current_max]
        if j + 1 == len(lists[i]):
            return best                   # one list is exhausted, cannot improve
        next_value = lists[i][j + 1]
        current_max = max(current_max, next_value)
        heapq.heappush(heap, (next_value, i, j + 1))

# smallest_range([[4, 10, 15, 24], [0, 9, 12, 20], [5, 18, 22, 30]]) -> [20, 24]
```

Time O(n log k), space O(k).

## Common pitfalls

- Comparing raw nodes or objects in the heap. Push a tuple whose first element is the sort key and include a tie breaker.
- Pushing the next element from the wrong list. Track the list index in the tuple.
- Re sorting after merging, which defeats the purpose.

## Practice problems

- Merge k Sorted Lists
- Kth Smallest Element in a Sorted Matrix
- Find K Pairs with Smallest Sums
- Smallest Range Covering Elements from K Lists
- Merge Sorted Array
- Ugly Number II
