# Binary Search

## Why This Topic Matters

Binary Search is one of the most misunderstood patterns. Students know it as "search in a sorted array," but interviewers use it far more broadly — as a technique for **eliminating half the search space at every step** whenever you can make a yes/no decision.

**Real-world applications:**
- Database index lookups (B-trees are binary search applied to disk storage)
- `git bisect` — finds the commit that introduced a bug by binary searching through history
- Load balancer threshold tuning
- Any "find the minimum value that satisfies condition X" problem

**Interview frequency:** High. Appears in ~25% of problems. Especially appears disguised in problems that don't obviously look like "search in an array."

---

## The Core Idea

Imagine guessing a number between 1 and 1,000,000. Your friend says "higher" or "lower" after each guess.

**Strategy A (Linear):** Guess 1, 2, 3, 4, ... → worst case: 1,000,000 guesses.

**Strategy B (Binary Search):** Guess 500,000. Too low → guess 750,000. Too high → guess 625,000. Repeat.

Each guess eliminates *half* the remaining possibilities. After 20 guesses, you've narrowed 1,000,000 possibilities to 1 (since 2²⁰ ≈ 1,000,000).

> *Binary search works on any space where you can make a binary decision that permanently eliminates half the remaining candidates.*

The space doesn't have to be a sorted array. It can be:
- A range of possible answers (binary search on the answer)
- A sorted matrix
- A rotated array
- Any monotonic function

**The condition for binary search to work:**
There must be a "left half" and "right half" such that the answer can only be in one of them based on a midpoint check.

---

## Pattern Recognition

### What clues should make you think of Binary Search?

✅ **Positive indicators:**
- The input array is **sorted** (or partially sorted / rotated)
- The problem asks you to find a target, minimum, or maximum in a sorted structure
- The problem says "find the smallest X such that condition(X) is true" — this is always binary search on the answer
- A brute force would try all values in a range — if you can test one value in O(n) and there are O(m) values, binary search brings O(m) → O(log m)
- Keywords: "sorted," "rotated," "minimum/maximum such that," "capacity," "speed," "feasibility"

❌ **When Binary Search is NOT appropriate:**
- The array is unsorted and you can't sort it
- The decision function isn't monotonic (can't split into "valid" and "invalid" halves)
- You need to find ALL occurrences, not just one

### The Binary Search on Answer Pattern
This is the advanced form. Recognize it when:
- The problem asks for a *minimum/maximum value* satisfying a constraint
- You can write a function `feasible(x)` that returns True/False
- As x increases, `feasible(x)` goes from False→True (or True→False)

---

## Brute Force Thinking

### Example Problem: Binary Search (Classic)
*Find target in sorted array. Return index, or -1.*

**Naive approach:** Scan every element.

```
for i in range(len(nums)):
    if nums[i] == target:
        return i
return -1
```

**Complexity:** O(n) — for 1,000,000 elements, up to 1,000,000 comparisons.
**Why it's slow:** We never use the fact that the array is sorted!

---

### Example Problem: Koko Eating Bananas
*Koko can eat k bananas/hour. Given piles and h hours, find minimum k to finish all bananas.*

**Naive approach:** Try every possible speed from 1 up.

```
for speed in range(1, max(piles) + 1):
    hours_needed = sum(ceil(pile/speed) for pile in piles)
    if hours_needed <= h:
        return speed
```

**Complexity:** O(max(piles) × n) — potentially billions of iterations!
**The key insight:** As speed increases, hours needed monotonically decreases. This is a perfect binary search on the answer.

---

## Discovering the Optimization

### Classic Binary Search: The elimination argument

**The insight:** At any point, our target can only be in a subrange `[left, right]`. The midpoint `mid` creates a binary choice:
- If `nums[mid] == target` → found it!
- If `nums[mid] < target` → target must be in the right half. Eliminate left half.
- If `nums[mid] > target` → target must be in the left half. Eliminate right half.

Each comparison halves the search space. After log₂(n) comparisons, the range collapses to nothing.

---

### The Tricky Part: Left vs. Right Boundary

Standard binary search has three variants, and getting the boundaries wrong is the most common source of bugs:

1. **Find exact match** — the standard form
2. **Find leftmost occurrence** — keep searching left even after finding target
3. **Find rightmost occurrence** — keep searching right even after finding target

The key question: **when should we stop, and which side do we favor?**

---

### Binary Search on the Answer: Recognizing Monotonicity

For Koko Eating Bananas:
- At speed 1, it takes a very long time (infeasible for small h)
- At speed max(piles), it takes exactly len(piles) hours (fastest possible)

As speed increases: hours needed decreases. There's a threshold — the minimum feasible speed.

```
speed:   1  2  3  4  5  6  7  8 ...
feasible: F  F  F  T  T  T  T  T ...
```

Binary search finds this threshold in O(log(max_speed)) iterations.

---

## Visual Walkthrough

### Classic Binary Search

```
nums = [-1, 0, 3, 5, 9, 12], target = 9

left=0, right=5
  mid = 0 + (5-0)//2 = 2
  nums[2] = 3 < 9 → target in right half
  left = 3

left=3, right=5
  mid = 3 + (5-3)//2 = 4
  nums[4] = 9 == target → FOUND
  return 4 ✓
```

---

### Search in Rotated Sorted Array

```
nums = [4, 5, 6, 7, 0, 1, 2], target = 0

left=0, right=6
  mid = 3, nums[3] = 7
  Left half [4,5,6,7] is sorted
  target=0 NOT in [4..7] → search right half
  left = 4

left=4, right=6
  mid = 5, nums[5] = 1
  Left half [0,1] is sorted
  target=0 IS in [0..1] → search left half
  right = 4

left=4, right=4
  mid = 4, nums[4] = 0 == target → FOUND
  return 4 ✓
```

---

## Final Optimal Solution

### Classic Binary Search
```python
def search(nums: list[int], target: int) -> int:
    left, right = 0, len(nums) - 1

    while left <= right:
        mid = left + (right - left) // 2   # Avoids integer overflow

        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid + 1    # Target in right half
        else:
            right = mid - 1   # Target in left half

    return -1   # Not found
```

### Find Leftmost Occurrence (Lower Bound)
```python
def search_leftmost(nums: list[int], target: int) -> int:
    left, right = 0, len(nums) - 1
    result = -1

    while left <= right:
        mid = left + (right - left) // 2

        if nums[mid] == target:
            result = mid      # Record, but keep searching left!
            right = mid - 1
        elif nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1

    return result
```

### Search in Rotated Sorted Array
```python
def search_rotated(nums: list[int], target: int) -> int:
    left, right = 0, len(nums) - 1

    while left <= right:
        mid = left + (right - left) // 2

        if nums[mid] == target:
            return mid

        # Determine which half is sorted
        if nums[left] <= nums[mid]:   # Left half is sorted
            if nums[left] <= target < nums[mid]:
                right = mid - 1   # Target in left sorted half
            else:
                left = mid + 1    # Target in right half
        else:   # Right half is sorted
            if nums[mid] < target <= nums[right]:
                left = mid + 1    # Target in right sorted half
            else:
                right = mid - 1   # Target in left half

    return -1
```

### Koko Eating Bananas (Binary Search on Answer)
```python
import math

def min_eating_speed(piles: list[int], h: int) -> int:
    # Answer range: minimum speed=1, maximum speed=max pile
    left, right = 1, max(piles)

    def can_finish(speed: int) -> bool:
        """Can Koko eat all bananas at this speed within h hours?"""
        hours = sum(math.ceil(pile / speed) for pile in piles)
        return hours <= h

    # Find the LEFTMOST speed where can_finish is True
    while left < right:
        mid = left + (right - left) // 2
        if can_finish(mid):
            right = mid     # mid works, but maybe something smaller works too
        else:
            left = mid + 1  # mid doesn't work, need higher speed

    return left   # Smallest valid speed
```

---

## Complexity Breakdown

### Classic Binary Search

| Operation | Time | Space | Why |
|-----------|------|-------|-----|
| Each iteration halves search space | O(log n) total | O(1) | Halving: n → n/2 → n/4 → ... → 1 takes log₂(n) steps |
| **Overall** | **O(log n)** | **O(1)** | |

### Koko Eating Bananas

| Operation | Time | Space | Why |
|-----------|------|-------|-----|
| Binary search over speed range | O(log(max_pile)) | O(1) | |
| `can_finish` check | O(n) | O(1) | Sum over all piles |
| **Overall** | **O(n log M)** | **O(1)** | M = max pile size |

**Intuition for log:** log₂(10⁹) ≈ 30. Binary searching over a billion possible values takes only ~30 iterations. This is why binary search is so powerful for "search on the answer" problems.

---

## Common Mistakes

### 1. Integer overflow in mid calculation
```python
# WRONG in languages with integer overflow (Java, C++):
mid = (left + right) // 2   # left + right can overflow!

# CORRECT (works everywhere, good habit in Python too):
mid = left + (right - left) // 2
```

### 2. Infinite loop: wrong boundary update
```python
# WRONG: if mid is never excluded, loop runs forever
while left < right:
    mid = (left + right) // 2
    if condition:
        left = mid   # Never progresses! left stays at mid

# CORRECT: always exclude mid when it can't be the answer
    left = mid + 1  # Or right = mid (when mid could be the answer)
```

### 3. Confusing `left < right` vs `left <= right`
```python
# Use left <= right when: you want to check the last element (classic search)
# Use left < right when: you're narrowing to a single answer (left=right is the answer)
```

### 4. Rotated array: wrong sorted-half detection
```python
# Use <= not < for the left boundary check
if nums[left] <= nums[mid]:   # ← must be <=, not <
    # Left half is sorted (handles duplicates edge case)
```

### 5. Binary search on answer: wrong boundary shrink
```python
# When can_finish(mid) is True and we want the MINIMUM:
right = mid       # Keep mid — it might BE the answer
# NOT right = mid - 1 — that would skip the answer!
```

---

## Pattern Template

```python
# TEMPLATE 1: Classic binary search (exact match)
def binary_search(nums, target):
    left, right = 0, len(nums) - 1
    while left <= right:
        mid = left + (right - left) // 2
        if nums[mid] == target:   return mid
        elif nums[mid] < target:  left = mid + 1
        else:                     right = mid - 1
    return -1

# TEMPLATE 2: Find leftmost valid position
# (first True in: [F, F, F, T, T, T])
def binary_search_left(nums, condition):
    left, right = 0, len(nums) - 1
    result = -1
    while left <= right:
        mid = left + (right - left) // 2
        if condition(mid):
            result = mid
            right = mid - 1   # Keep searching left for earlier True
        else:
            left = mid + 1
    return result

# TEMPLATE 3: Binary search on answer
# Finds minimum value satisfying feasibility condition
def binary_search_on_answer(lo, hi, feasible):
    while lo < hi:
        mid = lo + (hi - lo) // 2
        if feasible(mid):
            hi = mid        # mid works, try smaller
        else:
            lo = mid + 1    # mid doesn't work, need bigger
    return lo   # lo == hi: the minimum feasible value
```
---

## Practice Problems

### 🟢 Easy: Binary Search (LeetCode #704)
**Why this problem:** The pure form. No tricks, no rotation. If students can't do this cleanly with proper boundary handling, they're not ready for harder variants. Perfect for mastering the template.

**Concept reinforced:** Core template; mid calculation; left/right updates.

---

### 🟡 Medium: Find Minimum in Rotated Sorted Array (LeetCode #153)
**Why this problem:** Forces students to think about which half of a rotated array contains the minimum, using the sorted-half detection trick. Teaches "what does the midpoint tell me about the structure?"

**Concept reinforced:** Rotated array logic; sorted-half determination.

---

### 🟠 Medium/Hard: Median of Two Sorted Arrays (LeetCode #4)
**Why this problem:** The hardest binary search problem. Requires binary searching on the *partition point* of the smaller array, then deriving the partition of the larger array. Hard companies (Google, Amazon, Uber) use this as a final-round problem. If you can solve this cleanly, binary search has no more secrets.

**Concept reinforced:** Binary search on partition; combining two sorted halves; edge case handling.

---
