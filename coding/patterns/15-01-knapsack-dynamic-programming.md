# Pattern 15: 0/1 Knapsack (Dynamic Programming)

## What it is

A large family of problems where you process items one at a time and, for each, choose to include it or exclude it, subject to a capacity or target constraint. "0/1" means each item is used at most once. The state is `(item index, remaining capacity)` and the answer for a state depends only on smaller states, so you memoize or fill a table.

## When to use it

- You make a binary include or exclude choice per item.
- There is a budget, weight limit, or target sum.
- The question asks for a maximum value, whether a target is reachable, or a count of ways.
- Greedy fails because a locally good pick blocks a better overall set.

## Template

Top down with memoization:

```python
from functools import lru_cache

def knapsack(weights, values, capacity):
    n = len(weights)

    @lru_cache(maxsize=None)
    def best(i, remaining):
        if i == n or remaining == 0:
            return 0
        skip = best(i + 1, remaining)
        take = 0
        if weights[i] <= remaining:
            take = values[i] + best(i + 1, remaining - weights[i])
        return max(skip, take)

    return best(0, capacity)
```

Bottom up with a 1D array (iterate capacity downward so each item is used once):

```python
def knapsack_table(weights, values, capacity):
    dp = [0] * (capacity + 1)
    for i in range(len(weights)):
        for cap in range(capacity, weights[i] - 1, -1):
            dp[cap] = max(dp[cap], values[i] + dp[cap - weights[i]])
    return dp[capacity]
```

Time O(n * capacity), space O(capacity).

## Worked example 1: partition equal subset sum

Return `True` if `nums` can be split into two subsets with equal sums.

```python
def can_partition(nums):
    total = sum(nums)
    if total % 2:
        return False
    target = total // 2
    possible = {0}
    for num in nums:
        possible |= {s + num for s in possible if s + num <= target}
        if target in possible:
            return True
    return target in possible

# can_partition([1, 5, 11, 5]) -> True   ([1, 5, 5] and [11])
```

Time O(n * target), space O(target).

## Worked example 2: target sum

Assign a `+` or `-` sign to each number so the signed total equals `target`. Count the ways.

```python
def find_target_sum_ways(nums, target):
    total = sum(nums)
    if abs(target) > total or (total + target) % 2:
        return 0
    subset_sum = (total + target) // 2       # numbers we mark positive

    dp = [0] * (subset_sum + 1)
    dp[0] = 1
    for num in nums:
        for s in range(subset_sum, num - 1, -1):
            dp[s] += dp[s - num]
    return dp[subset_sum]

# find_target_sum_ways([1, 1, 1, 1, 1], 3) -> 5
```

Time O(n * subset_sum), space O(subset_sum). The sign problem reduces to counting subsets with a fixed sum.

## Worked example 3: last stone weight II

Stones are smashed in pairs; the result is the absolute difference. Return the smallest possible weight of the last stone. This is: split the stones into two groups and minimize the difference of their sums.

```python
def last_stone_weight_ii(stones):
    total = sum(stones)
    target = total // 2
    dp = [False] * (target + 1)
    dp[0] = True
    for stone in stones:
        for s in range(target, stone - 1, -1):
            dp[s] = dp[s] or dp[s - stone]
    for s in range(target, -1, -1):
        if dp[s]:
            return total - 2 * s

# last_stone_weight_ii([2, 7, 4, 1, 8, 1]) -> 1
```

Time O(n * total), space O(total).

## Related: unbounded knapsack

If an item can be reused any number of times (coin change, rod cutting), iterate the capacity dimension upward instead of downward:

```python
def coin_change_min(coins, amount):
    dp = [0] + [float("inf")] * amount
    for coin in coins:
        for value in range(coin, amount + 1):
            dp[value] = min(dp[value], dp[value - coin] + 1)
    return dp[amount] if dp[amount] != float("inf") else -1
```

## Common pitfalls

- Iterating capacity upward in a 0/1 problem, which lets one item be used many times.
- Forgetting the parity or feasibility check before computing a subset sum.
- Using recursion without memoization, which is exponential.

## Practice problems

- Partition Equal Subset Sum
- Target Sum
- Last Stone Weight II
- Ones and Zeroes
- Coin Change
- Coin Change II
- Combination Sum IV
- Perfect Squares
- Word Break
