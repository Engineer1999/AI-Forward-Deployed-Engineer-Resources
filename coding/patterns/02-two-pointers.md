# Pattern 2: Two Pointers

## What it is

You keep two indices into a sequence and move them based on the current values. The pointers can start at opposite ends and move toward each other, or start together and move at different speeds. Because each pointer advances at most `n` times, the scan is O(n) and uses O(1) extra space.

## When to use it

- The array or string is sorted, or becomes useful when sorted.
- You look for a pair, triplet, or subsequence that meets a target.
- You compare elements from both ends, or you partition a list in place.
- The brute force is a nested loop over all pairs.

## Templates

Opposite ends, converging:

```python
def pair_with_target_sum(nums, target):
    left, right = 0, len(nums) - 1
    while left < right:
        current = nums[left] + nums[right]
        if current == target:
            return [left, right]
        if current < target:
            left += 1        # need a larger sum
        else:
            right -= 1        # need a smaller sum
    return [-1, -1]
```

Same start, different speed (used to overwrite in place):

```python
def move_unique_to_front(nums):
    slow = 0
    for fast in range(len(nums)):
        if fast == 0 or nums[fast] != nums[fast - 1]:
            nums[slow] = nums[fast]
            slow += 1
    return slow   # length of the deduplicated prefix
```

## Worked example 1: triplets that sum to zero

Return all unique triplets `[a, b, c]` from `nums` with `a + b + c == 0`.

```python
def three_sum(nums):
    nums.sort()
    triplets = []
    for i in range(len(nums) - 2):
        if i > 0 and nums[i] == nums[i - 1]:
            continue                       # skip duplicate anchors
        left, right = i + 1, len(nums) - 1
        while left < right:
            total = nums[i] + nums[left] + nums[right]
            if total == 0:
                triplets.append([nums[i], nums[left], nums[right]])
                left += 1
                right -= 1
                while left < right and nums[left] == nums[left - 1]:
                    left += 1
                while left < right and nums[right] == nums[right + 1]:
                    right -= 1
            elif total < 0:
                left += 1
            else:
                right -= 1
    return triplets

# three_sum([-3, 0, 1, 2, -1, 1, -2]) -> [[-3, 1, 2], [-2, 0, 2], [-2, 1, 1], [-1, 0, 1]]
```

Time O(n^2), space O(1) beyond the sort and the output.

## Worked example 2: squares of a sorted array

Given an array sorted in non decreasing order, return an array of the squares of each number, also sorted.

```python
def sorted_squares(nums):
    n = len(nums)
    result = [0] * n
    left, right = 0, n - 1
    for pos in range(n - 1, -1, -1):
        if abs(nums[left]) > abs(nums[right]):
            result[pos] = nums[left] * nums[left]
            left += 1
        else:
            result[pos] = nums[right] * nums[right]
            right -= 1
    return result

# sorted_squares([-4, -1, 0, 3, 10]) -> [0, 1, 9, 16, 100]
```

Time O(n), space O(n) for the output.

## Worked example 3: is a string a palindrome, ignoring non alphanumerics

```python
def is_palindrome(s):
    left, right = 0, len(s) - 1
    while left < right:
        while left < right and not s[left].isalnum():
            left += 1
        while left < right and not s[right].isalnum():
            right -= 1
        if s[left].lower() != s[right].lower():
            return False
        left += 1
        right -= 1
    return True

# is_palindrome("A man, a plan, a canal: Panama") -> True
```

Time O(n), space O(1).

## Common pitfalls

- Sorting when the problem needs original indices. Save the indices first or use a different pattern.
- Not skipping duplicates in problems that ask for unique results.
- Moving both pointers when only one should move.

## Practice problems

- Two Sum II Input Array Is Sorted
- Remove Duplicates from Sorted Array
- 3Sum Closest
- 4Sum
- Container With Most Water
- Trapping Rain Water
- Sort Colors (Dutch national flag)
- Backspace String Compare
