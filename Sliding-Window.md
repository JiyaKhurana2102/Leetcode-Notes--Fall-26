# Sliding Window

## Why This Topic Matters

Sliding Window is the technique interviewers use to test whether you can **avoid recomputing things you already know**. It's the difference between a programmer who re-reads the whole document every time they need information, versus one who remembers what they just read.

**Real-world applications:**
- Network packet analysis: monitoring the last N seconds of traffic
- Stock market analysis: computing rolling averages
- Text processing: finding patterns in DNA sequences (bioinformatics)
- Real-time analytics: keeping a running window of recent metrics

**Interview frequency:** High. Appears in about 30% of array/string problems. Once you recognize the pattern, many seemingly hard problems become straightforward.

---

## The Core Idea

Imagine a camera recording a long parade. The camera has a fixed frame — it can only show a portion of the parade at once. As the parade moves, the camera doesn't reset and scan from the beginning each time. It just slides forward, showing the next section.

That's a sliding window.

> *A sliding window maintains a "view" into an array or string, tracking some property of the elements in that view, and slides one step at a time — **adding one element on the right and optionally removing one on the left** — without re-examining elements it's already processed.*

**Why is this faster than brute force?**

Brute force: For every possible starting position, compute the property of the window from scratch.

Sliding window: Compute it once, then *update* it incrementally as the window shifts.

Instead of recomputing `sum(window)` from scratch each time (O(k) per window), we do `window_sum += new_right - old_left` (O(1) per step).

---

## Pattern Recognition

### What clues should make you think of Sliding Window?

✅ **Positive indicators:**
- The problem involves a **contiguous subarray or substring**
- Keywords: "subarray," "substring," "window," "contiguous," "consecutive"
- You're looking for the **longest/shortest** subarray satisfying some condition
- You're computing something over a **fixed-size window** (max sum of k elements)
- The condition is about **count/frequency** within a window (at most k distinct characters)
- The "valid window" condition is monotonic — adding more elements makes it worse (or better), not random

❌ **When Sliding Window is NOT the right tool:**
- You need non-contiguous elements (use DP or backtracking)
- The problem is about pairs, not subarrays (consider Two Pointers or HashMap)
- The window condition doesn't have a clear "shrink" strategy
- You need to restart from scratch based on arbitrary conditions

### Fixed vs. Variable Window

**Fixed window:** Size k is given. Window always has exactly k elements.
- Example: "Find max sum of any k consecutive elements"

**Variable window:** Find the smallest/largest window satisfying a condition.
- Example: "Longest substring without repeating characters"
- Example: "Smallest subarray with sum ≥ target"

---

## Brute Force Thinking

### Example Problem: Maximum Sum Subarray of Size K
*Given an array and integer k, find the maximum sum of any contiguous subarray of size k.*

**Naive approach:**
For every possible starting position, compute the sum of the next k elements.

```
max_sum = 0
for i from 0 to n-k:
    window_sum = 0
    for j from i to i+k-1:
        window_sum += nums[j]
    max_sum = max(max_sum, window_sum)
return max_sum
```

**Complexity:**
- Time: O(n × k) — for each of the n windows, compute sum of k elements
- Space: O(1)

**Why it's wasteful:**
When the window slides from position `i` to `i+1`, we remove `nums[i]` and add `nums[i+k]`. But the brute force recomputes the sum of the middle k-1 elements from scratch! Those elements didn't change!

---

### Example Problem: Longest Substring Without Repeating Characters
**Naive approach:**
For every possible starting position `i`, expand `j` until you find a repeat.

```
max_length = 0
for i from 0 to n-1:
    seen = set()
    for j from i to n-1:
        if nums[j] in seen:
            break
        seen.add(nums[j])
    max_length = max(max_length, j - i)
return max_length
```

**Complexity:** O(n²) — for each of n starts, scan up to n characters.

---

## Discovering the Optimization

### Fixed Window: The Sliding Sum

**Key observation:** When we slide the window one step to the right, **only two elements change**:
- One element **leaves** the left side
- One element **enters** the right side
- Everything in the middle stays the same!

So instead of recomputing the entire sum, we just:
```
new_sum = old_sum - nums[left] + nums[right]
```

One subtraction, one addition. O(1) update instead of O(k) recompute.

---

### Variable Window: The Shrink-When-Invalid Strategy

For "longest valid window" problems, the insight is different:

**Ask yourself:** "When my window becomes invalid, what's the minimum I need to remove to make it valid again?"

For "no repeating characters": When we add a character that's already in the window, we need to shrink from the left until that duplicate is removed.

**Key insight:**
> *We never need to go back. Once the right pointer passes a position, we never reconsider it as a starting point for a longer valid window. The left pointer only moves right.*

Both pointers move right only — so each element is added once and removed at most once — giving O(n) total.

---

## Visual Walkthrough

### Maximum Sum of k=3 Elements

```
nums = [2, 1, 5, 1, 3, 2], k = 3

Initialize: compute first window
window_sum = 2+1+5 = 8
max_sum = 8

[2, 1, 5, 1, 3, 2]
 ^^^^^^^^^^^
 left=0, right=2

Slide: remove nums[0]=2, add nums[3]=1
window_sum = 8 - 2 + 1 = 7
max_sum = 8 (unchanged)

[2, 1, 5, 1, 3, 2]
    ^^^^^^^^^^^
    left=1, right=3

Slide: remove nums[1]=1, add nums[4]=3
window_sum = 7 - 1 + 3 = 9
max_sum = 9 ← new maximum!

[2, 1, 5, 1, 3, 2]
       ^^^^^^^^^^^
       left=2, right=4

Slide: remove nums[2]=5, add nums[5]=2
window_sum = 9 - 5 + 2 = 6
max_sum = 9 (unchanged)

[2, 1, 5, 1, 3, 2]
          ^^^^^^^^^^^
          left=3, right=5

Answer: 9
```

---

### Longest Substring Without Repeating Characters

```
s = "abcabcbb"

left=0, right=0, window={a}, len=1
left=0, right=1, window={a,b}, len=2
left=0, right=2, window={a,b,c}, len=3  ← max so far
left=0, right=3: add 'a' — DUPLICATE!
  'a' is at index 0, which is inside window [0..3]
  Shrink: remove s[left]=s[0]='a', left becomes 1
  left=1, right=3, window={b,c,a}, len=3

left=1, right=4: add 'b' — DUPLICATE!
  'b' is at index 1, which is inside window [1..4]
  Shrink: remove s[1]='b', left=2
  left=2, right=4, window={c,a,b}, len=3

left=2, right=5: add 'c' — DUPLICATE!
  'c' is at index 2 = left, so shrink
  left=3, right=5, window={a,b,c}, len=3

left=3, right=6: add 'b' — DUPLICATE!
  'b' is at index 4, inside window
  Shrink: remove s[3]='a', left=4
           remove s[4]='b', left=5
  left=5, right=6, window={c,b}, len=2

left=5, right=7: add 'b' — DUPLICATE!
  Shrink: left=6, right=7, window={b}, len=1

Final max = 3
```

---

## Final Optimal Solution

### Maximum Sum of Size-K Subarray
```python
def max_sum_subarray(nums: list[int], k: int) -> int:
    # Compute the first window
    window_sum = sum(nums[:k])
    max_sum = window_sum

    # Slide: for each new right element, remove leftmost element
    for right in range(k, len(nums)):
        left = right - k   # The element sliding out of the window
        window_sum += nums[right] - nums[left]
        max_sum = max(max_sum, window_sum)

    return max_sum
```

### Longest Substring Without Repeating Characters
```python
def length_of_longest_substring(s: str) -> int:
    char_index = {}   # Maps character to its most recent index in window
    left = 0
    max_length = 0

    for right, char in enumerate(s):
        # If char is in window (its last index is >= left pointer)
        if char in char_index and char_index[char] >= left:
            # Jump left pointer past the previous occurrence
            left = char_index[char] + 1

        # Record/update this character's most recent position
        char_index[char] = right

        # Update max with current window size
        max_length = max(max_length, right - left + 1)

    return max_length
```

### Minimum Size Subarray Sum
```python
def min_subarray_len(target: int, nums: list[int]) -> int:
    left = 0
    window_sum = 0
    min_length = float('inf')   # Start with "infinity" — no valid window yet

    for right in range(len(nums)):
        window_sum += nums[right]   # Expand window

        # Shrink window from left as long as it's still valid
        while window_sum >= target:
            min_length = min(min_length, right - left + 1)
            window_sum -= nums[left]
            left += 1

    return 0 if min_length == float('inf') else min_length
```

---

## Complexity Breakdown

### Fixed Window (Max Sum)

| Operation | Time | Space | Why |
|-----------|------|-------|-----|
| Initial window setup | O(k) | O(1) | Sum first k elements |
| Slide n-k times | O(n-k) | O(1) | Each slide: O(1) arithmetic |
| **Overall** | **O(n)** | **O(1)** | One pass, constant memory |

### Variable Window (Longest Substring)

| Operation | Time | Space | Why |
|-----------|------|-------|-----|
| Right pointer scan | O(n) | — | Each char visited once |
| Left pointer moves | O(n) total | — | Each char removed at most once |
| HashMap storage | — | O(min(n, 26)) | At most alphabet-size entries |
| **Overall** | **O(n)** | **O(k)** | k = size of character set |

**The amortized argument:** Even though we have a `while` loop inside the `for` loop, each element is added exactly once (when `right` reaches it) and removed at most once (when `left` passes it). Total operations ≤ 2n = O(n).

---

## Common Mistakes

### 1. Re-computing the window from scratch
```python
# WRONG: O(n²) — defeats the purpose of sliding window
for right in range(k, len(nums)):
    window_sum = sum(nums[right-k+1:right+1])   # Recomputing!

# CORRECT: O(n) — update incrementally
window_sum += nums[right] - nums[right - k]
```

### 2. Forgetting to handle the case where no valid window exists
```python
# For min subarray sum:
min_length = float('inf')
# ... sliding window logic ...
return 0 if min_length == float('inf') else min_length
# If you forget the check, you return infinity!
```

### 3. Wrong condition for "char in window" check
```python
# WRONG: char might be in char_index but outside the current window
if char in char_index:
    left = char_index[char] + 1   # Could move left backward!

# CORRECT: only care if char is within current window [left, right]
if char in char_index and char_index[char] >= left:
    left = char_index[char] + 1
```

### 4. Off-by-one in window size
```python
# Window from index left to right (inclusive)
# Size = right - left + 1, NOT right - left
length = right - left + 1   # ✓
```

### 5. Moving left past right
```python
# In some shrink conditions, always guard:
while left <= right and condition:
    left += 1
```

---

## Pattern Template

```python
# TEMPLATE 1: Fixed-size sliding window
def fixed_window(nums, k):
    # Initialize first window
    window_val = compute(nums[:k])
    result = window_val

    for right in range(k, len(nums)):
        left = right - k
        # Update window: remove nums[left], add nums[right]
        window_val = update(window_val, nums[left], nums[right])
        result = best(result, window_val)

    return result

# TEMPLATE 2: Variable-size sliding window (find LONGEST valid)
def variable_window_longest(s):
    left = 0
    window_state = {}   # Track what's in the window
    max_length = 0

    for right in range(len(s)):
        # Expand: add s[right] to window
        add_to_window(window_state, s[right])

        # Shrink: while invalid, remove s[left]
        while not is_valid(window_state):
            remove_from_window(window_state, s[left])
            left += 1

        # Window is now valid — record result
        max_length = max(max_length, right - left + 1)

    return max_length

# TEMPLATE 3: Variable-size sliding window (find SHORTEST valid)
def variable_window_shortest(nums, target):
    left = 0
    window_sum = 0
    min_length = float('inf')

    for right in range(len(nums)):
        # Expand
        window_sum += nums[right]

        # Shrink while valid (keep shrinking to find minimum)
        while window_sum >= target:
            min_length = min(min_length, right - left + 1)
            window_sum -= nums[left]
            left += 1

    return 0 if min_length == float('inf') else min_length
```

---

## Practice Problems

### 🟢 Easy: Best Time to Buy and Sell Stock (LeetCode #121)
**Why this problem:** Teaches the fixed-window mindset applied to "profit = sell price - buy price." The "window" is from the minimum price seen so far to the current day. Reinforces "update incrementally."

**Concept reinforced:** Fixed window / running minimum; single-pass O(n) solution.

---

### 🟡 Medium: Longest Substring Without Repeating Characters (LeetCode #3)
**Why this problem:** The canonical variable-window problem. Must use a HashMap to track character positions. Excellent for practicing the "expand right, shrink left" paradigm.

**Concept reinforced:** Variable window with HashMap state; the `char_index >= left` guard.

---

### 🟠 Medium/Hard: Minimum Window Substring (LeetCode #76)
**Why this problem:** The hardest "contains all required characters" variant. Requires tracking frequencies of required characters AND a count of how many requirements are currently satisfied. Demanding but exactly what top-company interviews test.

**Concept reinforced:** Variable window with complex validity conditions; two-frequency-map approach.

---
