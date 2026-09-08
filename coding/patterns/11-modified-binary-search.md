# Pattern 11: Modified Binary Search

## What it is

Binary search on any search space where you can decide, in O(1) or O(log n), which half to keep. The space is often a sorted array, but it can also be a range of answers, a rotated array, or an infinite stream. Each step halves the space, so the total work is O(log n).

## When to use it

- The array is sorted or rotated sorted.
- The problem asks for a boundary: first or last position, smallest value that works, ceiling or floor.
- You can phrase the question as "is `x` feasible?" and feasibility is monotonic (once true, it stays true).

## Templates

Classic search:

```python
def binary_search(nums, target):
    lo, hi = 0, len(nums) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if nums[mid] == target:
            return mid
        if nums[mid] < target:
            lo = mid + 1
        else:
            hi = mid - 1
    return -1
```

Leftmost boundary (first index where a predicate becomes true):

```python
def lower_bound(lo, hi, is_ok):
    while lo < hi:
        mid = (lo + hi) // 2
        if is_ok(mid):
            hi = mid
        else:
            lo = mid + 1
    return lo   # smallest value in [lo, hi] with is_ok(value) True
```

## Worked example 1: search in a rotated sorted array

A sorted array is rotated at an unknown pivot. Find `target` in O(log n).

```python
def search_rotated(nums, target):
    lo, hi = 0, len(nums) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if nums[mid] == target:
            return mid
        if nums[lo] <= nums[mid]:            # left half is sorted
            if nums[lo] <= target < nums[mid]:
                hi = mid - 1
            else:
                lo = mid + 1
        else:                               # right half is sorted
            if nums[mid] < target <= nums[hi]:
                lo = mid + 1
            else:
                hi = mid - 1
    return -1

# search_rotated([4, 5, 6, 7, 0, 1, 2], 0) -> 4
```

Time O(log n), space O(1).

## Worked example 2: find the smallest letter greater than target

`letters` is sorted and wraps around. Return the smallest letter strictly greater than `target`; if none, return `letters[0]`.

```python
def next_greatest_letter(letters, target):
    lo, hi = 0, len(letters)
    while lo < hi:
        mid = (lo + hi) // 2
        if letters[mid] <= target:
            lo = mid + 1
        else:
            hi = mid
    return letters[lo % len(letters)]

# next_greatest_letter(["c", "f", "j"], "j") -> "c"
```

Time O(log n), space O(1).

## Worked example 3: binary search on the answer, Koko eating bananas

Koko eats `speed` bananas per hour from one pile. Given `piles` and `h` hours, return the smallest `speed` that finishes all piles within `h` hours.

```python
import math

def min_eating_speed(piles, h):
    def hours_needed(speed):
        return sum(math.ceil(pile / speed) for pile in piles)

    lo, hi = 1, max(piles)
    while lo < hi:
        mid = (lo + hi) // 2
        if hours_needed(mid) <= h:          # feasible, try slower
            hi = mid
        else:
            lo = mid + 1
    return lo

# min_eating_speed([3, 6, 7, 11], 8) -> 4
```

Time O(n log(max pile)), space O(1). The search space here is the answer range, not the array.

## Common pitfalls

- Mixing `while lo <= hi` (returns an exact index) with `while lo < hi` (converges to a boundary). Pick one style per problem.
- `mid = (lo + hi) // 2` can skew low; for a rightmost boundary use `mid = (lo + hi + 1) // 2` to avoid an infinite loop.
- Updating `lo = mid` or `hi = mid` without shrinking the range, which loops forever.

## Practice problems

- Binary Search
- Search Insert Position
- Find First and Last Position of Element in Sorted Array
- Search in Rotated Sorted Array
- Find Minimum in Rotated Sorted Array
- Search a 2D Matrix
- Koko Eating Bananas
- Capacity To Ship Packages Within D Days
- Split Array Largest Sum
- Median of Two Sorted Arrays
