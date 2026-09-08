# Pattern 10: Subsets

## What it is

A family of problems where the answer is every combination, permutation, or subset of the input. Two approaches cover almost all of them:

1. **Iterative expansion**: start with an empty result and, for each input element, extend every existing partial answer.
2. **Backtracking**: build one candidate at a time with a recursive function that makes a choice, recurses, then undoes the choice.

Both explore an exponential space, so expect O(2^n) or O(n!) time.

## When to use it

- "Return all subsets", "all permutations", "all combinations", "all ways to ...".
- The output is a list of lists whose length grows exponentially with `n`.
- You need to enumerate rather than count. If you only need a count or an optimum, look at dynamic programming instead.

## Template: iterative subsets

```python
def subsets(nums):
    result = [[]]
    for num in nums:
        result += [subset + [num] for subset in result]
    return result

# subsets([1, 2, 3]) -> [[], [1], [2], [1, 2], [3], [1, 3], [2, 3], [1, 2, 3]]
```

## Template: backtracking

```python
def backtrack_all(nums):
    result = []

    def backtrack(start, path):
        result.append(list(path))          # every node is a valid subset
        for i in range(start, len(nums)):
            path.append(nums[i])
            backtrack(i + 1, path)
            path.pop()                     # undo the choice

    backtrack(0, [])
    return result
```

## Worked example 1: subsets with duplicates

The input may contain repeats. Return all unique subsets.

```python
def subsets_with_dup(nums):
    nums.sort()
    result = [[]]
    start = 0
    for i in range(len(nums)):
        first = 0 if i == 0 or nums[i] != nums[i - 1] else start
        start = len(result)
        for j in range(first, start):
            result.append(result[j] + [nums[i]])
    return result

# subsets_with_dup([1, 2, 2]) -> [[], [1], [2], [1, 2], [2, 2], [1, 2, 2]]
```

Sorting groups duplicates; when a value repeats, only extend the subsets that were created in the previous round, which avoids duplicate subsets.

## Worked example 2: permutations

```python
def permutations(nums):
    result = []

    def backtrack(path, remaining):
        if not remaining:
            result.append(list(path))
            return
        for i in range(len(remaining)):
            path.append(remaining[i])
            backtrack(path, remaining[:i] + remaining[i + 1:])
            path.pop()

    backtrack([], nums)
    return result

# permutations([1, 2, 3]) -> 6 lists
```

Time O(n * n!), space O(n) for the recursion plus the output.

## Worked example 3: combination sum

Return all unique combinations of `candidates` that sum to `target`. Each candidate may be reused any number of times.

```python
def combination_sum(candidates, target):
    candidates.sort()
    result = []

    def backtrack(start, remaining, path):
        if remaining == 0:
            result.append(list(path))
            return
        for i in range(start, len(candidates)):
            if candidates[i] > remaining:
                break                      # sorted, so the rest are too big
            path.append(candidates[i])
            backtrack(i, remaining - candidates[i], path)   # i, not i + 1, allows reuse
            path.pop()

    backtrack(0, target, [])
    return result

# combination_sum([2, 3, 6, 7], 7) -> [[2, 2, 3], [7]]
```

## Common pitfalls

- Appending `path` instead of a copy `list(path)`. The list is mutated later.
- Forgetting to `pop()` after the recursive call.
- Passing `start` versus `i + 1` versus `i` wrong: `i + 1` means no reuse and no revisiting earlier elements, `i` allows reuse.
- Generating duplicates when the input has repeats. Sort first and skip `nums[i] == nums[i - 1]` at the same tree depth.

## Practice problems

- Subsets
- Subsets II
- Permutations
- Permutations II
- Combinations
- Combination Sum
- Combination Sum II
- Letter Combinations of a Phone Number
- Generate Parentheses
- Palindrome Partitioning
