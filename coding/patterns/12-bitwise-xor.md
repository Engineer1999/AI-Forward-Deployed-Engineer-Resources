# Pattern 12: Bitwise XOR

## What it is

XOR (`^`) has three properties that solve a class of problems in O(n) time and O(1) space:

- `x ^ x == 0` (a value cancels itself)
- `x ^ 0 == x` (zero is the identity)
- XOR is commutative and associative (order does not matter)

So if you XOR a group of numbers where every value appears an even number of times except one, everything cancels and you are left with that one value.

## When to use it

- Find the single number when all others appear twice.
- Find a missing number in `0..n`.
- Swap two values without a temp variable.
- Compare two structures for equality of contents regardless of order.

## Worked example 1: single number

Every element appears twice except one. Find it.

```python
def single_number(nums):
    result = 0
    for num in nums:
        result ^= num
    return result

# single_number([4, 1, 2, 1, 2]) -> 4
```

Time O(n), space O(1). `sum(set(nums)) * 2 - sum(nums)` also works but uses O(n) space.

## Worked example 2: missing number

`nums` holds `n` distinct values from `0` to `n`. XOR all indices `0..n` with all values; pairs cancel and the missing number remains.

```python
def missing_number(nums):
    result = len(nums)                    # start with n, the largest index
    for i, num in enumerate(nums):
        result ^= i ^ num
    return result

# missing_number([3, 0, 1]) -> 2
```

Time O(n), space O(1).

## Worked example 3: two single numbers

Exactly two elements appear once, every other appears twice. Find both.

```python
def two_single_numbers(nums):
    xor_all = 0
    for num in nums:
        xor_all ^= num                    # this equals a ^ b for the two singles

    # isolate the lowest set bit, where a and b differ
    diff_bit = xor_all & (-xor_all)

    a = b = 0
    for num in nums:
        if num & diff_bit:
            a ^= num
        else:
            b ^= num
    return [a, b]

# two_single_numbers([1, 2, 1, 3, 2, 5]) -> [3, 5] (order may vary)
```

Time O(n), space O(1). Splitting the numbers by a differing bit isolates each single number into its own group.

## Worked example 4: complement of a number

Flip every bit of a positive integer within its own bit length.

```python
def find_complement(num):
    mask = 1
    while mask < num:
        mask = (mask << 1) | 1            # all ones, as wide as num
    return num ^ mask

# find_complement(5) -> 2   (101 -> 010)
```

## Bit tricks worth memorizing

```python
x & 1                 # 1 if x is odd
x >> 1                # x // 2
x << 1                # x * 2
x & (x - 1)           # clears the lowest set bit
x & (-x)              # isolates the lowest set bit
bin(x).count("1")     # number of set bits (population count)
x ^ y                 # differs-in bits of x and y
```

## Common pitfalls

- Expecting XOR to help when values appear three times or more. Use a count map or bit counting per position instead.
- Sign issues with `-x` on fixed width integers in other languages. Python integers are arbitrary precision, so `x & (-x)` is safe here.

## Practice problems

- Single Number
- Single Number II
- Single Number III
- Missing Number
- Number Complement
- Find the Difference
- XOR Operation in an Array
- Sum of Two Integers (add without +)
