# Pattern 3: Fast and Slow Pointers

## What it is

Also called the tortoise and hare. Two pointers move through a sequence at different speeds, usually one step versus two steps. If the structure contains a cycle, the fast pointer eventually laps the slow one and they meet. If there is no cycle, the fast pointer reaches the end first. When they meet at the midpoint, the slow pointer sits at the middle.

## When to use it

- Linked list problems that ask about a cycle, the cycle length, or the cycle start.
- Finding the middle of a linked list in one pass.
- Any problem framed as following "next" links, including number sequences like happy numbers.
- You need O(1) space, so you cannot use a visited set.

## Templates

Cycle detection:

```python
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:
            return True
    return False
```

Find the middle node:

```python
def middle_node(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow   # for even length, this is the second of the two middles
```

## Worked example 1: start of the cycle

Return the node where the cycle begins, or `None` if there is no cycle.

```python
def cycle_start(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:
            break
    else:
        return None                 # loop ended without a meeting

    # move one pointer to head, then advance both at the same speed
    pointer = head
    while pointer is not slow:
        pointer = pointer.next
        slow = slow.next
    return pointer
```

Why it works: when the two meet, the distance from the head to the cycle start equals the distance from the meeting point to the cycle start, measured around the loop. Time O(n), space O(1).

## Worked example 2: happy number

A number is happy if repeatedly replacing it with the sum of the squares of its digits eventually reaches 1. Numbers that are not happy fall into a cycle. Detect this without a set.

```python
def is_happy(n):
    def next_number(x):
        total = 0
        while x > 0:
            x, digit = divmod(x, 10)
            total += digit * digit
        return total

    slow = fast = n
    while True:
        slow = next_number(slow)
        fast = next_number(next_number(fast))
        if fast == 1:
            return True
        if slow == fast:
            return False

# is_happy(19) -> True,  is_happy(2) -> False
```

Time O(log n) per step and a bounded number of steps, space O(1).

## Worked example 3: palindrome linked list

Check whether a singly linked list reads the same forward and backward, using O(1) space.

```python
def is_palindrome_list(head):
    # find the middle
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next

    # reverse the second half
    prev = None
    while slow:
        slow.next, prev, slow = prev, slow, slow.next

    # compare halves
    left, right = head, prev
    while right:                     # the reversed half is the shorter or equal one
        if left.val != right.val:
            return False
        left = left.next
        right = right.next
    return True
```

Time O(n), space O(1). Restore the list afterward if the caller still needs it.

## Common pitfalls

- Checking `fast` but not `fast.next` before calling `fast.next.next`.
- Assuming the meeting point is the cycle start. It is not; you need the second phase.
- Forgetting that for even length lists the slow pointer lands on the second middle.

## Practice problems

- Linked List Cycle
- Linked List Cycle II
- Middle of the Linked List
- Find the Duplicate Number
- Circular Array Loop
- Remove Nth Node From End of List
