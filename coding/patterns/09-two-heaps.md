# Pattern 9: Two Heaps

## What it is

You split a collection into a lower half and an upper half. A max heap holds the smaller numbers so its top is the largest of the small side. A min heap holds the larger numbers so its top is the smallest of the large side. Keeping the two heaps balanced in size lets you read the median, or the boundary between halves, in O(1) after O(log n) inserts.

Python only has a min heap (`heapq`), so simulate a max heap by pushing negated values.

## When to use it

- "Find the median" of a running stream of numbers.
- You repeatedly need the middle element, or the largest of one group and the smallest of another.
- Scheduling problems that pair the current smallest and largest remaining items.

## Template

```python
import heapq

class MedianFinder:
    def __init__(self):
        self.small = []   # max heap (store negatives): the lower half
        self.large = []   # min heap: the upper half

    def add_num(self, num):
        heapq.heappush(self.small, -num)
        # move the largest of the small side to the large side
        heapq.heappush(self.large, -heapq.heappop(self.small))
        # rebalance so small has the same size or one more element
        if len(self.large) > len(self.small):
            heapq.heappush(self.small, -heapq.heappop(self.large))

    def find_median(self):
        if len(self.small) > len(self.large):
            return -self.small[0]
        return (-self.small[0] + self.large[0]) / 2
```

## Worked example 1: sliding window median

Return the median of every window of size `k` as it slides across `nums`.

The two heap idea still applies, but sliding a window means removing an arbitrary old value, which a plain heap cannot do in O(log n). Two clean options:

1. **Lazy deletion**: keep a map of values that should be gone, and skip them when they surface at a heap top. Rebalance by counts, not by raw sizes.
2. **A balanced BST or order statistics structure**. In Python the practical choice is `sortedcontainers.SortedList`, which supports O(log k) insert, remove, and index.

The `SortedList` version is short and hard to get wrong:

```python
from sortedcontainers import SortedList

def median_sliding_window(nums, k):
    window = SortedList(nums[:k])
    medians = []
    for i in range(k, len(nums) + 1):
        if k % 2:
            medians.append(float(window[k // 2]))
        else:
            medians.append((window[k // 2 - 1] + window[k // 2]) / 2)
        if i == len(nums):
            break
        window.remove(nums[i - k])   # drop the element leaving the window
        window.add(nums[i])          # add the element entering the window
    return medians

# median_sliding_window([1, 3, -1, -3, 5, 3, 6, 7], 3) -> [1, -1, -1, 3, 5, 6]
```

Time O(n log k), space O(k). If the interviewer bans external libraries, fall back to the two heaps with lazy deletion described above.

## Worked example 2: IPO, maximize capital

You can do at most `k` projects. Project `i` needs `capital[i]` to start and yields `profits[i]`. Start with `w` capital. Return the maximum final capital.

```python
import heapq

def find_maximized_capital(k, w, profits, capital):
    projects = sorted(zip(capital, profits))   # by capital needed
    available = []                              # max heap of profits (negated)
    i = 0
    for _ in range(k):
        while i < len(projects) and projects[i][0] <= w:
            heapq.heappush(available, -projects[i][1])
            i += 1
        if not available:
            break
        w += -heapq.heappop(available)
    return w

# find_maximized_capital(2, 0, [1, 2, 3], [0, 1, 1]) -> 4
```

Time O(n log n), space O(n). One heap sorts by cost, the other picks the best affordable profit.

## Common pitfalls

- Forgetting to negate when pushing to or reading from the simulated max heap.
- Letting the two heaps drift out of balance. Rebalance after every insert.
- Using a heap when you actually need to delete arbitrary elements often; consider a sorted container or lazy deletion.

## Practice problems

- Find Median from Data Stream
- Sliding Window Median
- IPO
- Find Right Interval
- Maximize Capital
