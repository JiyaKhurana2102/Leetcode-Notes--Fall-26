# Two Pointers

## Why This Topic Matters

Two Pointers is one of the most elegant optimization patterns in all of programming. It shows up constantly in interviews because it demonstrates a fundamental insight: **sometimes you can exploit the structure of your data to avoid redundant work entirely**.

**Real-world applications:**
- Merging sorted arrays (used in merge sort, database merges)
- Finding pairs in sorted datasets
- Removing duplicates from sorted arrays (used in database deduplication)
- Palindrome verification in text editors and DNA sequence analysis

**Interview frequency:** Very high. Appears in array, string, and linked list problems. Often the key to converting an O(n²) brute force into O(n).

---

## The Core Idea

Picture two people standing at opposite ends of a hallway, walking toward each other. They'll meet somewhere in the middle. This is the essence of Two Pointers.

More specifically:

> *Two Pointers is the art of maintaining two positions in your data simultaneously, and moving them strategically based on what you observe — so you never have to consider all pairs.*

**Analogy 1: The Squeeze**
Imagine squeezing a tube of toothpaste from both ends toward the middle. Each squeeze eliminates a section you no longer need to consider.

**Analogy 2: The Dictionary**
Finding a word in a dictionary: you open to the middle, decide whether the word is in the first or second half, then "point" to a new middle. One pointer eliminates half the search space.

**The key insight that makes this work:**
If the array is sorted (or has some exploitable ordering), when we check a pair `(left, right)` and the result is too small, we *know* we need a bigger value — so we move `left` right. When it's too big, we move `right` left. We never need to check that pair again.

---

## Pattern Recognition

### What clues should make you think of Two Pointers?

✅ **Positive indicators:**
- The array or string is **sorted** (or can be sorted without losing information)
- You're looking for a **pair** of elements satisfying some condition (sum = target, difference, etc.)
- The problem says "find pair," "find triplet," or involves comparing elements from two ends
- You're working with a **palindrome** (compare character from front and back)
- You're **merging** two sorted arrays
- You need to partition or rearrange elements **in-place** with O(1) space
- Keywords: "sorted array," "remove duplicates," "reverse," "palindrome"

❌ **When Two Pointers is NOT appropriate:**
- The array is unsorted and you can't sort it (indices matter, like Two Sum)
- You need to consider all possible pairs, not just pairs at the boundaries
- You're looking for a subarray (use Sliding Window instead)
- The problem requires tracking state for a window of elements (Sliding Window)

---

## Brute Force Thinking

### Example Problem: Two Sum II (Sorted Array)
*Given a sorted array, find two numbers that add up to target. Return their 1-indexed positions.*

**The naive approach:**
Check every pair.

```
for i from 0 to n-1:
    for j from i+1 to n-1:
        if nums[i] + nums[j] == target:
            return [i+1, j+1]
```

**Complexity:**
- Time: O(n²) — check every combination
- Space: O(1)

**Why it's too slow:** For n = 10,000, that's 50 million pair checks. And crucially — **we're ignoring the fact that the array is sorted!** That's like being given a dictionary and reading every page instead of using alphabetical order.

---

### Example Problem: Container With Most Water
*Given heights `[1,8,6,2,5,4,8,3,7]`, find two lines that form a container holding the most water.*

**Brute Force:**
```
max_water = 0
for i from 0 to n-1:
    for j from i+1 to n-1:
        width = j - i
        height = min(heights[i], heights[j])
        max_water = max(max_water, width * height)
return max_water
```

**Complexity:**
- Time: O(n²)
- Space: O(1)

---

## Discovering the Optimization

### Two Sum II: Using the sorted property

**Ask yourself:** "When I check `nums[left] + nums[right]` and the sum is too small, what do I know?"

Answer: I need a bigger sum. Since the array is sorted:
- Making `right` smaller would only make the sum smaller (wrong direction)
- Making `left` bigger could make the sum larger ✓

So when `sum < target` → move `left` right.

**When the sum is too big?**
- Making `left` bigger makes the sum even bigger (wrong direction)
- Making `right` smaller could reduce the sum ✓

So when `sum > target` → move `right` left.

**The key insight:**
> *Every time we move a pointer, we permanently eliminate an entire row or column of possibilities from our mental "table of all pairs." We never need to revisit it.*

---

### Container With Most Water: The greedy squeeze

**The observation:** The container area = `min(h[left], h[right]) × (right - left)`.

When we move a pointer inward, width always decreases by 1. So to potentially increase area, we must increase height. Which pointer should we move?

**If we move the taller side:** We definitely lose width, and height can only stay the same or decrease (since the new height is still bounded by the shorter side). We can never do better.

**If we move the shorter side:** We lose width, but we *might* gain a taller height, which could increase area. This is our only chance of improvement.

**The greedy rule:** Always move the pointer pointing to the shorter line.

---

## Visual Walkthrough

### Two Sum II

```
nums = [2, 7, 11, 15], target = 9

left=0, right=3
  sum = 2 + 15 = 17 > 9
  Too big → move right left

left=0, right=2
  sum = 2 + 11 = 13 > 9
  Too big → move right left

left=0, right=1
  sum = 2 + 7 = 9 == 9 ✓
  Return [1, 2] (1-indexed)
```

Notice: we made only 3 comparisons instead of 6 (all pairs). We skipped `(7,11)`, `(7,15)`, and `(11,15)` — we knew they couldn't work!

---

### Three Sum

```
nums = [-4, -1, -1, 0, 1, 2]  (sorted!)
target = 0

Fix i=0 (num=-4), two pointer on rest:
  left=1(-1), right=5(2): -4+-1+2=-3 < 0 → move left
  left=2(-1), right=5(2): -4+-1+2=-3 < 0 → move left
  left=3(0),  right=5(2): -4+0+2=-2  < 0 → move left
  left=4(1),  right=5(2): -4+1+2=-1  < 0 → move left
  left=5 == right=5: done with i=0

Fix i=1 (num=-1), two pointer on rest:
  left=2(-1), right=5(2): -1+-1+2=0 == 0 ✓ → add [-1,-1,2]
    (skip duplicates on both ends)
  left=3(0),  right=4(1): -1+0+1=0  == 0 ✓ → add [-1,0,1]

Fix i=2 (num=-1): same as i=1, skip (duplicate)

Fix i=3 (num=0), two pointer on rest:
  left=4(1), right=5(2): 0+1+2=3 > 0 → move right
  left=4(1) == right=4: done

Result: [[-1,-1,2], [-1,0,1]]
```

---

## Final Optimal Solution

### Two Sum II
```python
def two_sum(numbers: list[int], target: int) -> list[int]:
    left, right = 0, len(numbers) - 1

    while left < right:
        total = numbers[left] + numbers[right]

        if total == target:
            return [left + 1, right + 1]   # Problem is 1-indexed
        elif total < target:
            left += 1    # Need bigger sum → advance left pointer
        else:
            right -= 1   # Need smaller sum → retreat right pointer

    return []   # No solution (problem guarantees one exists)
```

### Three Sum
```python
def three_sum(nums: list[int]) -> list[list[int]]:
    nums.sort()   # Must sort first!
    result = []

    for i in range(len(nums) - 2):
        # Skip duplicate values for the fixed element
        if i > 0 and nums[i] == nums[i - 1]:
            continue

        left, right = i + 1, len(nums) - 1

        while left < right:
            total = nums[i] + nums[left] + nums[right]

            if total == 0:
                result.append([nums[i], nums[left], nums[right]])
                # Skip duplicates for left and right pointers
                while left < right and nums[left] == nums[left + 1]:
                    left += 1
                while left < right and nums[right] == nums[right - 1]:
                    right -= 1
                left += 1
                right -= 1

            elif total < 0:
                left += 1    # Sum too small
            else:
                right -= 1   # Sum too big

    return result
```

### Valid Palindrome
```python
def is_palindrome(s: str) -> bool:
    # Clean: keep only alphanumeric, lowercase
    cleaned = [ch.lower() for ch in s if ch.isalnum()]

    left, right = 0, len(cleaned) - 1
    while left < right:
        if cleaned[left] != cleaned[right]:
            return False
        left += 1
        right -= 1
    return True
```

---

## Complexity Breakdown

### Two Sum II

| Operation | Time | Space | Why |
|-----------|------|-------|-----|
| Two pointer scan | O(n) | O(1) | Each pointer moves at most n times total |
| **Overall** | **O(n)** | **O(1)** | Linear scan, no extra memory |

### Three Sum

| Operation | Time | Space | Why |
|-----------|------|-------|-----|
| Sorting | O(n log n) | O(1) | Python's Timsort |
| Outer loop × inner two-pointer | O(n²) | O(1) | n iterations × O(n) each |
| **Overall** | **O(n²)** | **O(n)** | Output space for results |

*Note: O(n²) is actually optimal for 3Sum — any algorithm must potentially output O(n²) triplets.*

---

## Common Mistakes

### 1. Forgetting to sort first
```python
# Three Sum REQUIRES a sorted array to use two pointers
# Never forget: nums.sort() before the main logic
```

### 2. Infinite loop in duplicate skipping
```python
# WRONG: can cause infinite loop or index error
while nums[left] == nums[left + 1]:   # No bounds check!
    left += 1

# CORRECT: always guard the bounds
while left < right and nums[left] == nums[left + 1]:
    left += 1
```

### 3. Off-by-one in the outer loop of Three Sum
```python
# WRONG: i goes to len(nums)-1, leaving nothing for left/right
for i in range(len(nums)):

# CORRECT: leave at least 2 elements for the two pointers
for i in range(len(nums) - 2):
```

### 4. Moving the wrong pointer in Container With Most Water
```python
# WRONG intuition: "move the taller one to find taller"
if heights[left] > heights[right]:
    left += 1   # This is wrong!

# CORRECT: move the SHORTER one (it's the limiting factor)
if heights[left] < heights[right]:
    left += 1
else:
    right -= 1
```

### 5. Using Two Pointers on an unsorted array when you need indices
If Two Sum asked for indices and you sorted, your indices are now wrong. Always check whether sorting is allowed for the specific problem.

---

## Pattern Template

```python
# TEMPLATE 1: Opposite-end two pointers (sorted array)
def two_pointer_opposite(arr, target):
    left, right = 0, len(arr) - 1

    while left < right:
        result = compute(arr[left], arr[right])

        if result == target:
            # Found answer
            return ...
        elif result < target:
            left += 1     # Need to increase result
        else:
            right -= 1    # Need to decrease result

    return ...

# TEMPLATE 2: Same-direction two pointers (fast/slow)
def two_pointer_same_dir(arr):
    slow = 0   # "write" pointer (valid window end)

    for fast in range(len(arr)):   # "read" pointer
        if condition(arr[fast]):
            arr[slow] = arr[fast]
            slow += 1

    return slow   # Length of valid portion

# TEMPLATE 3: Palindrome check
def is_palindrome(s):
    left, right = 0, len(s) - 1
    while left < right:
        if s[left] != s[right]:
            return False
        left += 1
        right -= 1
    return True
```

---

## Practice Problems

### 🟢 Easy: Valid Palindrome (LeetCode #125)
**Why this problem:** Perfect entry point. Two pointers from both ends, simple comparison. Reinforces the "move both inward" pattern with a clear stopping condition.

**Concept reinforced:** Classic opposite-end two pointer; string cleaning.

---

### 🟡 Medium: Container With Most Water (LeetCode #11)
**Why this problem:** Teaches the *greedy choice* behind which pointer to move. Students must reason about why moving the shorter line is always correct. Builds intuition for non-obvious pointer movement rules.

**Concept reinforced:** Greedy pointer movement; proving correctness by elimination.

---

### 🟠 Medium/Hard: Trapping Rain Water (LeetCode #42)
**Why this problem:** A harder variant where you need to track `max_left` and `max_right` as the pointers move. Forces students to think about what information each pointer needs to carry. Classic hard problem that appears frequently at top companies.

**Concept reinforced:** Two pointers with auxiliary state; thinking about what each pointer "knows."

---
