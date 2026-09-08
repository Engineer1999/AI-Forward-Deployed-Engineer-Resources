# Pattern 1: Sliding Window

## What it is

A window is a contiguous range of a list or string, tracked by a left and a right index. You slide the right edge to grow the window and the left edge to shrink it, keeping some running state (a sum, a count, a character frequency map) up to date as you go. This replaces a brute force scan of every subarray, which is O(n^2) or worse, with a single O(n) pass.

## When to use it

- The problem asks for the longest, shortest, or best contiguous subarray or substring.
- You see phrases like "subarray of size k", "at most k distinct", "no repeating characters", "sum equals target".
- A brute force solution recomputes overlapping ranges.

## Two forms

1. **Fixed size window**: the window is always exactly `k` wide. Slide it one step at a time.
2. **Dynamic window**: the window grows until it breaks a condition, then shrinks from the left until it is valid again. You record the answer as the window changes.

## Templates

Fixed size window:

```python
def fixed_window(nums, k):
    window_sum = 0
    best = float("-inf")
    for right in range(len(nums)):
        window_sum += nums[right]
        if right >= k - 1:
            best = max(best, window_sum)
            window_sum -= nums[right - k + 1]  # drop the leftmost element
    return best
```

Dynamic window:

```python
def dynamic_window(s):
    left = 0
    state = {}          # whatever you need to track
    best = 0
    for right in range(len(s)):
        # 1. include s[right] in the window state
        state[s[right]] = state.get(s[right], 0) + 1

        # 2. shrink from the left while the window is invalid
        while window_is_invalid(state):
            state[s[left]] -= 1
            if state[s[left]] == 0:
                del state[s[left]]
            left += 1

        # 3. the window [left, right] is now valid, record the answer
        best = max(best, right - left + 1)
    return best
```

## Worked example 1: maximum sum of any subarray of size k

Given an integer array and a number `k`, return the largest sum among all subarrays of length `k`.

```python
def max_sum_subarray_of_size_k(nums, k):
    window_sum = 0
    best = 0
    for right in range(len(nums)):
        window_sum += nums[right]
        if right >= k - 1:
            best = max(best, window_sum)
            window_sum -= nums[right - k + 1]
    return best

# max_sum_subarray_of_size_k([2, 1, 5, 1, 3, 2], 3) -> 9  (5 + 1 + 3)
```

Time O(n), space O(1).

## Worked example 2: longest substring with no repeating characters

Return the length of the longest substring of `s` that contains no repeated character.

```python
def longest_substring_without_repeats(s):
    last_seen = {}
    left = 0
    best = 0
    for right, ch in enumerate(s):
        if ch in last_seen and last_seen[ch] >= left:
            left = last_seen[ch] + 1   # jump past the previous occurrence
        last_seen[ch] = right
        best = max(best, right - left + 1)
    return best

# longest_substring_without_repeats("abccabb") -> 3  ("abc")
```

Time O(n), space O(min(n, alphabet size)).

## Worked example 3: smallest subarray with a sum at least target

Return the length of the shortest contiguous subarray whose sum is greater than or equal to `target`, or 0 if none exists. All numbers are positive.

```python
def smallest_subarray_with_given_sum(target, nums):
    left = 0
    window_sum = 0
    best = float("inf")
    for right in range(len(nums)):
        window_sum += nums[right]
        while window_sum >= target:
            best = min(best, right - left + 1)
            window_sum -= nums[left]
            left += 1
    return best if best != float("inf") else 0

# smallest_subarray_with_given_sum(7, [2, 1, 5, 2, 3, 2]) -> 2  ([5, 2])
```

Time O(n), space O(1).

## Common pitfalls

- Forgetting to update the running state when the left edge moves.
- Using a dynamic window when the numbers can be negative; the "shrink while invalid" logic assumes growing the window moves the metric one way only. For negative numbers, prefer prefix sums with a hash map.
- Off by one errors on the fixed window: the leftmost index of a window ending at `right` is `right - k + 1`.

## Practice problems

- Maximum Average Subarray I
- Longest Substring with At Most K Distinct Characters
- Fruit Into Baskets
- Permutation in String
- Find All Anagrams in a String
- Minimum Window Substring
- Longest Repeating Character Replacement
- Maximum Number of Vowels in a Substring of Given Length
