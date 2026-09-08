# Pattern 6: In-place Reversal of a Linked List

## What it is

You rewire the `next` pointers of a singly linked list so that a section runs in the opposite direction, using only a few pointer variables. No new nodes and no arrays. The core move is a three variable rotation that flips one link per step.

## When to use it

- Reverse the whole list, a sublist between two positions, or every group of `k` nodes.
- Rotate or reorder a list where the transformation is a local pointer rewrite.
- The interviewer asks for O(1) extra space.

## Template

```python
def reverse_list(head):
    prev = None
    current = head
    while current:
        next_node = current.next   # save the rest of the list
        current.next = prev        # flip the link
        prev = current             # advance prev
        current = next_node        # advance current
    return prev                    # new head
```

Python lets you write the rotation in one line: `current.next, prev, current = prev, current, current.next`.

## Worked example 1: reverse a sublist

Reverse the nodes from position `p` to position `q`, counting from 1, and return the head.

```python
def reverse_between(head, p, q):
    if p == q:
        return head
    dummy = ListNode(0, head)
    before = dummy
    for _ in range(p - 1):
        before = before.next       # node just before the sublist

    prev = None
    current = before.next
    for _ in range(q - p + 1):
        current.next, prev, current = prev, current, current.next

    before.next.next = current     # old sublist head now points past the sublist
    before.next = prev             # connect the part before to the new sublist head
    return dummy.next

# 1 -> 2 -> 3 -> 4 -> 5, p=2, q=4  ->  1 -> 4 -> 3 -> 2 -> 5
```

Time O(n), space O(1).

## Worked example 2: reverse every k nodes

Reverse the list in groups of `k`. If the final group has fewer than `k` nodes, leave it as is.

```python
def reverse_k_group(head, k):
    # check there are at least k nodes left
    node = head
    for _ in range(k):
        if not node:
            return head
        node = node.next

    # reverse the first k nodes
    prev = None
    current = head
    for _ in range(k):
        current.next, prev, current = prev, current, current.next

    # head is now the tail of this group; link it to the reversed remainder
    head.next = reverse_k_group(current, k)
    return prev

# 1 -> 2 -> 3 -> 4 -> 5, k=2  ->  2 -> 1 -> 4 -> 3 -> 5
```

Time O(n), space O(n / k) for the recursion, or O(1) with an iterative version.

## Worked example 3: reorder list

Given `L0 -> L1 -> ... -> Ln`, reorder it to `L0 -> Ln -> L1 -> Ln-1 -> ...` in place.

```python
def reorder_list(head):
    if not head or not head.next:
        return

    # find the middle
    slow = fast = head
    while fast.next and fast.next.next:
        slow = slow.next
        fast = fast.next.next

    # reverse the second half
    second = slow.next
    slow.next = None
    prev = None
    while second:
        second.next, prev, second = prev, second, second.next
    second = prev

    # weave the two halves
    first = head
    while second:
        first.next, second.next, first, second = second, first.next, first.next, second.next
```

Time O(n), space O(1).

## Common pitfalls

- Losing the rest of the list by overwriting `current.next` before saving it.
- Forgetting to reconnect the reversed section to the nodes before and after it.
- Not using a dummy head when the first node may change.

## Practice problems

- Reverse Linked List
- Reverse Linked List II
- Reverse Nodes in k-Group
- Swap Nodes in Pairs
- Rotate List
- Reorder List
- Palindrome Linked List
