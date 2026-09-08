# Coding Patterns for Data Structures and Algorithms

A pattern based approach to data structure and algorithm interview questions, written in Python. Instead of memorizing hundreds of problems, you learn a smaller set of reusable patterns and how to recognize which one a problem needs.

This guide is a starting point. After you work through the patterns here, move on to curated question and solution sets in the parent [`coding/`](../) folder.

## Why patterns

Most interview coding questions are variations of a few core ideas. Once you can map a new problem to a pattern, you already know the data structure to reach for, the template to start from, and the likely time and space complexity. This turns a blank page into a fill in the blanks exercise.

## The 16 patterns

| # | Pattern | Use it when |
| --- | --- | --- |
| 1 | [Sliding Window](01-sliding-window.md) | You need the best or a valid contiguous subarray or substring of a list or string. |
| 2 | [Two Pointers](02-two-pointers.md) | The input is sorted or pairable and you compare or move from both ends or at two speeds. |
| 3 | [Fast and Slow Pointers](03-fast-and-slow-pointers.md) | You work with a linked list or sequence and need to detect a cycle or find a midpoint. |
| 4 | [Merge Intervals](04-merge-intervals.md) | The input is a set of intervals and you merge, insert, or find overlaps. |
| 5 | [Cyclic Sort](05-cyclic-sort.md) | The array holds numbers in a known range like 1 to n and you find missing or duplicate values. |
| 6 | [In-place Reversal of a Linked List](06-in-place-reversal-of-a-linked-list.md) | You reverse all or part of a linked list without extra space. |
| 7 | [Tree Breadth First Search](07-tree-breadth-first-search.md) | You traverse a tree level by level or need the shortest path in an unweighted graph. |
| 8 | [Tree Depth First Search](08-tree-depth-first-search.md) | You explore root to leaf paths, subtree sums, or recursive tree properties. |
| 9 | [Two Heaps](09-two-heaps.md) | You need the median or a balanced split of a stream of numbers. |
| 10 | [Subsets](10-subsets.md) | You generate all combinations, permutations, or subsets. |
| 11 | [Modified Binary Search](11-modified-binary-search.md) | The search space is sorted or monotonic, even if rotated or unknown in size. |
| 12 | [Bitwise XOR](12-bitwise-xor.md) | You find a unique or missing number using the properties of XOR. |
| 13 | [Top K Elements](13-top-k-elements.md) | You need the K largest, smallest, or most frequent items. |
| 14 | [K-way Merge](14-k-way-merge.md) | You merge or scan K sorted lists at once. |
| 15 | [0/1 Knapsack (Dynamic Programming)](15-01-knapsack-dynamic-programming.md) | You make include or exclude choices to hit a target under a constraint. |
| 16 | [Topological Sort (Graph)](16-topological-sort-graph.md) | You order tasks with dependencies in a directed acyclic graph. |

## How to use this guide

1. Read a pattern page top to bottom, including the template code.
2. Type the template from memory until it is automatic.
3. Solve the practice problems listed at the bottom of each page.
4. When you get stuck on a new problem, ask which pattern the clues point to before looking at a solution.

## Complexity notation used here

- `n` is the input size unless stated otherwise.
- Time and space are given in Big O, describing the worst case.
- Recursion stack space is counted as space.

## Credits

The pattern taxonomy follows the widely used community list popularized by the Grokking the Coding Interview course and this open source study guide by Chanda Abdul: [Several Coding Patterns for Solving Data Structures and Algorithms Problems during Interviews](https://github.com/Chanda-Abdul/Several-Coding-Patterns-for-Solving-Data-Structures-and-Algorithms-Problems-during-Interviews). The explanations, code, and examples in this folder are written from scratch for this repository.
