# Pattern 13: Top K Elements

## What it is

To find the `K` largest, smallest, or most frequent items you do not need to sort everything. Keep a heap of size `K`. For the `K` largest, use a min heap: when it exceeds size `K`, pop the smallest, so the heap always holds the current top `K` and its root is the `K`th largest. This is O(n log k) time and O(k) space.

## When to use it

- "Top K", "K largest", "K closest", "K most frequent", "Kth smallest".
- `K` is much smaller than `n`, so a full sort is wasteful.
- A streaming source where you cannot hold or sort all the data.

## Template

```python
import heapq

def k_largest(nums, k):
    min_heap = []
    for num in nums:
        heapq.heappush(min_heap, num)
        if len(min_heap) > k:
            heapq.heappop(min_heap)       # drop the smallest of the current top k
    return list(min_heap)                 # not sorted; heap order

# heapq.nlargest(k, nums) does the same thing in one call
```

## Worked example 1: Kth largest element in an array

```python
import heapq

def find_kth_largest(nums, k):
    min_heap = nums[:k]
    heapq.heapify(min_heap)
    for num in nums[k:]:
        if num > min_heap[0]:
            heapq.heapreplace(min_heap, num)   # pop then push in one step
    return min_heap[0]

# find_kth_largest([3, 2, 1, 5, 6, 4], 2) -> 5
```

Time O(n log k), space O(k). Quickselect gives O(n) average time if the interviewer asks.

## Worked example 2: K closest points to the origin

Return the `K` points with the smallest Euclidean distance to `(0, 0)`.

```python
import heapq

def k_closest(points, k):
    max_heap = []
    for x, y in points:
        dist = x * x + y * y
        heapq.heappush(max_heap, (-dist, x, y))   # negate for a max heap
        if len(max_heap) > k:
            heapq.heappop(max_heap)
    return [[x, y] for _, x, y in max_heap]

# k_closest([[1, 3], [-2, 2], [5, 8], [0, 1]], 2) -> [[-2, 2], [0, 1]]
```

Time O(n log k), space O(k). No need for a square root; compare squared distances.

## Worked example 3: top K frequent elements

```python
import heapq
from collections import Counter

def top_k_frequent(nums, k):
    counts = Counter(nums)
    return heapq.nlargest(k, counts.keys(), key=counts.get)

# top_k_frequent([1, 1, 1, 2, 2, 3], 2) -> [1, 2]
```

Time O(n log k), space O(n). Bucket sort by frequency gives O(n) if needed.

## Worked example 4: sort characters by frequency

```python
from collections import Counter

def frequency_sort(s):
    counts = Counter(s)
    return "".join(ch * freq for ch, freq in counts.most_common())

# frequency_sort("tree") -> "eert" or "eetr"
```

## Common pitfalls

- Using a max heap when you want the `K` largest. You want a min heap of size `K` so removals drop the wrong candidates.
- Sorting the whole input, which is O(n log n) and usually unnecessary.
- Forgetting that heap iteration order is not sorted order. Call `heapq.nsmallest` or sort the final `K` items if order matters.

## Practice problems

- Kth Largest Element in an Array
- Kth Largest Element in a Stream
- Top K Frequent Elements
- Top K Frequent Words
- K Closest Points to Origin
- Sort Characters By Frequency
- Find K Closest Elements
- Reorganize String
- Task Scheduler
