# Linked List

## Why This Topic Matters

Linked lists are where interviewers test your ability to **think in pointers** — to hold a mental model of a structure that isn't laid out neatly in memory like an array. They reveal whether you can manipulate references without losing track of nodes.

**Real-world applications:**
- Implementing stacks, queues, and deques under the hood
- Memory allocators (OS-level free lists)
- Undo history in applications (doubly linked lists)
- Hash map chaining for collision resolution
- Music playlists (skip forward/backward)

**Interview frequency:** High. Linked list problems test pointer manipulation skill directly. Many companies use them as "technical aptitude" problems — if you can handle complex pointer operations without bugs, they trust you with complex codebases.

---

## The Core Idea

An array is like a row of lockers, all in a line, numbered 1 through n. You know where locker 50 is because you can calculate it directly: "go 50 steps from the start."

A linked list is like a treasure hunt. Each clue (node) tells you where the next clue is. You can only get to clue 50 by following clues 1 → 2 → 3 → ... → 50. You can't jump directly.

Each **node** has two things:
1. A **value** (the data)
2. A **next pointer** (the address of the next node)

The last node points to `None` — the treasure hunt ends.

> *All linked list problems are really pointer manipulation problems. The "trick" is always figuring out how to update pointers in the right order without losing track of nodes.*

**The golden rule:** Before updating any `next` pointer, ask yourself: "Will I still be able to reach everything I need after this update?"

---

## Pattern Recognition

### What clues should make you think about Linked List techniques?

✅ **Positive indicators:**
- The input IS a linked list (obviously)
- You need to **detect a cycle** in any structure
- You need to find a **midpoint** or **k-th from end** efficiently
- You're **merging** two sorted sequences
- You need to **reverse** part or all of a sequence
- The problem mentions "in-place" reversal or rearrangement

### Key Linked List Patterns:
1. **Fast & Slow Pointers** — finding middle, detecting cycles
2. **Dummy Head** — simplifying edge cases for head insertion/deletion
3. **In-place Reversal** — reversing without extra space
4. **Two-pointer / Multi-pointer** — merging, finding k-th node

---

## Brute Force Thinking

### Example Problem: Reverse Linked List
*Reverse a linked list in-place.*

**Naive approach:** Copy all values to an array, reverse the array, rebuild the list.

```
values = []
current = head
while current:
    values.append(current.val)
    current = current.next

values.reverse()

current = head
for val in values:
    current.val = val
    current = current.next
return head
```

**Complexity:** O(n) time, O(n) space.
**Why it's suboptimal:** Uses O(n) extra memory. The in-place reversal needs only O(1) extra space.

---

### Example Problem: Linked List Cycle
*Detect if a linked list has a cycle.*

**Naive approach:** Record every visited node.

```
visited = set()
current = head
while current:
    if current in visited:
        return True   # Seen before = cycle!
    visited.add(current)
    current = current.next
return False
```

**Complexity:** O(n) time, O(n) space.
**Why we can do better:** Can we detect the cycle using O(1) space?

---

## Discovering the Optimization

### In-place Reversal: The Three-Pointer Dance

**The challenge:** To reverse `A → B → C → None` into `C → B → A → None`, we need to flip each arrow. But if we flip `A → B` to `A ← B`, we lose our reference to C!

**The insight:** We need to save `next` BEFORE we change the pointer.

```
Think of it as a relay race:
- prev: the last node we've fully reversed
- curr: the node we're currently reversing
- next_node: the node after curr (save it before we lose it!)

Step 1: Save next_node = curr.next  (save before losing!)
Step 2: curr.next = prev            (flip the arrow)
Step 3: prev = curr                 (advance prev)
Step 4: curr = next_node            (advance curr)
```

---

### Floyd's Cycle Detection: The Tortoise and Hare

**The brilliant insight for O(1) cycle detection:**
> *If there's a cycle, a fast runner and a slow runner on the same track will eventually meet.*

Imagine a circular running track. You run twice as fast as your friend. Eventually you'll lap them — you'll meet.

- **Slow pointer:** moves 1 step at a time
- **Fast pointer:** moves 2 steps at a time

If there's NO cycle: fast pointer reaches `None` first.
If there IS a cycle: fast pointer laps slow pointer — they meet inside the cycle.

**Why do they meet?** In a cycle of length L, the fast pointer gains 1 step on the slow pointer per iteration. They'll meet after at most L steps.

---

### Finding the Middle: Fast/Slow Pointers

If fast moves twice as fast as slow, when fast reaches the end, slow is at the middle.

- Array length 6: fast takes 3 steps to reach end, slow takes 3 → at position 3 (middle) ✓
- Array length 5: fast takes 2.5 → stops at position 4, slow at position 2 (middle) ✓

---

## Visual Walkthrough

### Reverse Linked List

```
Original: 1 → 2 → 3 → 4 → 5 → None

prev = None, curr = 1

Step 1:
  next_node = curr.next = 2
  curr.next = prev = None     ← flip: 1 → None
  prev = curr = 1
  curr = next_node = 2
  State: None ← 1    2 → 3 → 4 → 5

Step 2:
  next_node = 3
  curr.next = prev = 1        ← flip: 2 → 1
  prev = 2
  curr = 3
  State: None ← 1 ← 2    3 → 4 → 5

Step 3:
  next_node = 4
  curr.next = 2
  prev = 3, curr = 4
  State: None ← 1 ← 2 ← 3    4 → 5

Step 4:
  next_node = 5
  curr.next = 3
  prev = 4, curr = 5
  State: None ← 1 ← 2 ← 3 ← 4    5

Step 5:
  next_node = None
  curr.next = 4
  prev = 5, curr = None
  State: None ← 1 ← 2 ← 3 ← 4 ← 5

curr is None → done!
Return prev = 5

Result: 5 → 4 → 3 → 2 → 1 → None ✓
```

---

### Detect Cycle (Floyd's Algorithm)

```
List: 1 → 2 → 3 → 4 → 5 → 3 (cycle back to node 3)

slow, fast both start at 1.

Step 1: slow=2, fast=3
Step 2: slow=3, fast=5
Step 3: slow=4, fast=4  ← MEET! Cycle detected ✓
```

---

## Final Optimal Solution

### Reverse Linked List
```python
def reverse_list(head: ListNode) -> ListNode:
    prev = None
    curr = head

    while curr:
        next_node = curr.next    # 1. Save next (critical!)
        curr.next = prev         # 2. Flip the pointer
        prev = curr              # 3. Advance prev
        curr = next_node         # 4. Advance curr

    return prev   # prev is now the new head
```

### Detect Cycle
```python
def has_cycle(head: ListNode) -> bool:
    slow = fast = head

    while fast and fast.next:   # fast.next check: fast takes 2 steps
        slow = slow.next
        fast = fast.next.next

        if slow is fast:   # Use 'is' (identity), not '==' (value)!
            return True

    return False   # fast hit None → no cycle
```

### Merge Two Sorted Lists
```python
def merge_two_lists(list1: ListNode, list2: ListNode) -> ListNode:
    # Dummy head simplifies logic when building the result list
    dummy = ListNode(0)
    current = dummy

    while list1 and list2:
        if list1.val <= list2.val:
            current.next = list1
            list1 = list1.next
        else:
            current.next = list2
            list2 = list2.next
        current = current.next

    # Attach remaining nodes (at most one list has remaining elements)
    current.next = list1 if list1 else list2

    return dummy.next   # Skip the dummy head
```

### Find Middle of Linked List
```python
def find_middle(head: ListNode) -> ListNode:
    slow = fast = head

    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next

    return slow   # When fast reaches end, slow is at middle
```

### Remove Nth Node From End
```python
def remove_nth_from_end(head: ListNode, n: int) -> ListNode:
    dummy = ListNode(0)
    dummy.next = head
    left = dummy    # Left pointer: trails right by n+1
    right = head    # Right pointer: advances n steps first

    # Move right pointer n steps ahead
    for _ in range(n):
        right = right.next

    # Move both until right hits end
    while right:
        left = left.next
        right = right.next

    # left.next is the node to remove
    left.next = left.next.next

    return dummy.next
```

---

## Complexity Breakdown

### Reverse Linked List

| Operation | Time | Space | Why |
|-----------|------|-------|-----|
| Single pass | O(n) | O(1) | Visit each node once, 3 pointer updates per node |
| **Overall** | **O(n)** | **O(1)** | In-place reversal |

### Detect Cycle (Floyd's)

| Operation | Time | Space | Why |
|-----------|------|-------|-----|
| Fast pointer traversal | O(n) | O(1) | Meets slow in at most n steps |
| **Overall** | **O(n)** | **O(1)** | No extra storage needed |

### Merge Two Sorted Lists

| Operation | Time | Space | Why |
|-----------|------|-------|-----|
| Process m+n nodes | O(m+n) | O(1) | Each node appended once |
| **Overall** | **O(m+n)** | **O(1)** | In-place pointer manipulation |

---

## Common Mistakes

### 1. Losing next pointer during reversal
```python
# WRONG: lose reference to rest of list!
curr.next = prev
prev = curr
curr = curr.next   # curr.next is now prev — you've created a loop!

# CORRECT: save next BEFORE modifying curr.next
next_node = curr.next   # Save first!
curr.next = prev
prev = curr
curr = next_node
```

### 2. Using `==` instead of `is` for node identity
```python
# WRONG for cycle detection: compares values, not identity
if slow == fast:   # Two different nodes with same val would match!

# CORRECT: checks if they're the same object in memory
if slow is fast:
```

### 3. Forgetting to handle the dummy head return
```python
dummy = ListNode(0)
dummy.next = head
# ... manipulations ...
return dummy.next   # ← Always return dummy.next, NOT dummy!
```

### 4. Not checking `fast and fast.next` in fast/slow pointer
```python
# WRONG: fast.next.next crashes if fast.next is None
while fast:
    fast = fast.next.next   # AttributeError if fast.next is None!

# CORRECT: check both
while fast and fast.next:
    slow = slow.next
    fast = fast.next.next
```

### 5. Off-by-one in "remove Nth from end"
```python
# The right pointer needs to be n steps ahead
# AND we need one more step to stop BEFORE the target node
# Solution: use dummy head as starting point for left pointer
```

---

## Pattern Template

```python
# TEMPLATE 1: Reverse a linked list (iterative)
def reverse(head):
    prev, curr = None, head
    while curr:
        next_node = curr.next
        curr.next = prev
        prev, curr = curr, next_node
    return prev

# TEMPLATE 2: Fast & slow pointers
def fast_slow(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        # Check condition here (cycle, etc.)
    return slow  # At middle when fast reaches end

# TEMPLATE 3: Dummy head (safe list building)
def build_result(list1, list2):
    dummy = ListNode(0)
    curr = dummy
    while list1 and list2:
        # Attach the smaller node
        if list1.val <= list2.val:
            curr.next = list1
            list1 = list1.next
        else:
            curr.next = list2
            list2 = list2.next
        curr = curr.next
    curr.next = list1 or list2
    return dummy.next

# TEMPLATE 4: Two pointers with gap
def nth_from_end(head, n):
    dummy = ListNode(0, head)
    left, right = dummy, head
    for _ in range(n): right = right.next
    while right:
        left = left.next
        right = right.next
    left.next = left.next.next
    return dummy.next
```

---

## Practice Problems

### 🟢 Easy: Reverse Linked List (LeetCode #206)
**Why this problem:** The most fundamental pointer manipulation. If you can reverse a linked list cleanly with O(1) space, you demonstrate control over pointer operations. Many harder problems use this as a subroutine.

**Concept reinforced:** Three-pointer reversal; order of operations.

---

### 🟡 Medium: Linked List Cycle II (LeetCode #142)
**Why this problem:** Not just "detect cycle" (easy) but "find where the cycle begins" (medium). Requires the full Floyd's algorithm with the second phase: after meeting, reset one pointer to head, then move both one step at a time. They meet at the cycle start. Beautiful math.

**Concept reinforced:** Floyd's cycle detection; the mathematical proof of cycle entry point.

---

### 🟠 Medium/Hard: Reorder List (LeetCode #143)
**Why this problem:** Requires combining THREE techniques: find middle (fast/slow), reverse second half (in-place reversal), and merge two halves (merge). The problem itself is easy to understand, but the implementation requires flawlessly chaining three linked list operations. A great test of mastery.

**Concept reinforced:** Combining multiple linked list patterns; in-place manipulation.

---
