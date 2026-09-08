# Pattern 5: Cyclic Sort

## What it is

When an array contains `n` numbers taken from a known contiguous range such as `1` to `n` or `0` to `n - 1`, each value has a natural home index. Cyclic sort walks the array and repeatedly swaps the current value into its correct slot until every position holds the right number. After that, one more pass finds anything missing, duplicated, or out of place. All of this is O(n) time and O(1) space.

## When to use it

- The array holds `n` integers in the range `1..n` or `0..n-1`, possibly with one duplicate or one gap.
- The problem asks for a missing number, a duplicate, the first missing positive, or all numbers in a range that are absent.
- You are tempted to use a hash set but the interviewer wants O(1) space.

## Template

```python
def cyclic_sort(nums):
    i = 0
    while i < len(nums):
        target_index = nums[i] - 1          # value v belongs at index v - 1
        if nums[i] != nums[target_index]:
            nums[i], nums[target_index] = nums[target_index], nums[i]
        else:
            i += 1
    return nums

# cyclic_sort([3, 1, 5, 4, 2]) -> [1, 2, 3, 4, 5]
```

The key move is: do not advance `i` until `nums[i]` is home. Swapping only when the destination does not already hold the value keeps the loop finite even with duplicates.

## Worked example 1: find the missing number

`nums` contains `n` distinct numbers from `0` to `n`. Exactly one is missing. Return it.

```python
def find_missing_number(nums):
    i, n = 0, len(nums)
    while i < n:
        target = nums[i]
        if target < n and nums[i] != nums[target]:
            nums[i], nums[target] = nums[target], nums[i]
        else:
            i += 1
    for index in range(n):
        if nums[index] != index:
            return index
    return n

# find_missing_number([4, 0, 3, 1]) -> 2
```

Time O(n), space O(1).

## Worked example 2: find all missing numbers

`nums` has `n` values in `1..n`, some appearing twice and some not at all. Return every number in `1..n` that does not appear.

```python
def find_disappeared_numbers(nums):
    i, n = 0, len(nums)
    while i < n:
        target_index = nums[i] - 1
        if nums[i] != nums[target_index]:
            nums[i], nums[target_index] = nums[target_index], nums[i]
        else:
            i += 1
    return [index + 1 for index in range(n) if nums[index] != index + 1]

# find_disappeared_numbers([2, 3, 1, 8, 2, 3, 5, 1]) -> [4, 6, 7]
```

Time O(n), space O(1) beyond the output.

## Worked example 3: find the duplicate number

`nums` has `n + 1` integers in `1..n`. Exactly one value is repeated. Return it without modifying the array conceptually (cyclic sort does mutate; if mutation is banned use fast and slow pointers on the index sequence instead).

```python
def find_duplicate(nums):
    i = 0
    while i < len(nums):
        if nums[i] != i + 1:
            target_index = nums[i] - 1
            if nums[i] != nums[target_index]:
                nums[i], nums[target_index] = nums[target_index], nums[i]
            else:
                return nums[i]        # destination already holds this value
        else:
            i += 1
    return -1

# find_duplicate([1, 4, 4, 3, 2]) -> 4
```

Time O(n), space O(1).

## Common pitfalls

- Advancing `i` after a swap. You must recheck the new `nums[i]`.
- Infinite loops from swapping equal values. Guard with `nums[i] != nums[target_index]`.
- Forgetting the range offset. Values in `1..n` map to index `value - 1`; values in `0..n-1` map to index `value`.

## Practice problems

- Missing Number
- Find All Numbers Disappeared in an Array
- Find the Duplicate Number
- Find All Duplicates in an Array
- First Missing Positive
- Set Mismatch
- Couples Holding Hands
