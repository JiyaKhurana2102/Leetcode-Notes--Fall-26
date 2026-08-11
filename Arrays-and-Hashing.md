# Arrays & Hashing

## Why This Topic Matters

Arrays and Hashing is the foundation of nearly everything else in LeetCode. Interviewers ask these questions to test whether you understand the most fundamental trade-off in all of computer science: **time vs. space**.

**Real-world applications:**
- Database indexing (hash maps power every database index)
- Caching (your browser cache, Redis, CDNs)
- Counting word frequencies in documents (search engines)
- Detecting duplicate transactions in financial systems

**Interview frequency:** Extremely high. These patterns appear in 60–70% of problems at all difficulty levels — even hard graph and DP problems often use a hash map as a component.

---

## The Core Idea

Imagine you're a librarian. Someone asks: "Is there a copy of *Harry Potter* in this library?"

**Without a catalog (brute force):** You walk every single aisle, checking every shelf. If the library has 10,000 books, you check up to 10,000 books. This is O(n) search.

**With a catalog (hash map):** You look up "Harry Potter" in the catalog. It tells you instantly: "Aisle 7, Shelf 3." This is O(1) lookup.

The catalog is a **hash map**. You trade some extra space (the catalog takes up room) in exchange for dramatically faster lookups.

**The core idea of Arrays & Hashing problems:**
> *"I've seen this value before. Instead of searching for it again, I'll remember it in a hash map so I can look it up in O(1)."*

This single observation converts countless O(n²) solutions into O(n).

---

## Pattern Recognition

### What clues should make you think of hashing?

✅ **Positive indicators:**
- The problem asks about **duplicates**, **counts**, or **frequencies**
- You need to check if you've **seen a value before**
- The problem involves **grouping** items that share a property
- You're doing a **two-pass** over data and need to remember the first pass
- The problem involves finding **pairs** or **complements** (e.g., two numbers that sum to a target)
- "Contains duplicate", "anagram", "frequency", "most common" in the problem title

❌ **When hashing is NOT the right tool:**
- The input is already sorted and you need O(1) space (use two pointers instead)
- You need to find a range or contiguous subarray (consider sliding window)
- The problem requires ordering — sets and dicts are unordered by default
- Space is extremely constrained (O(1) space required)

---

## Brute Force Thinking

### Example Problem: Two Sum
*Given an array `nums` and a target integer, return the indices of the two numbers that add up to the target.*

**The naive approach:**
Students naturally think: "I'll check every possible pair." That means for each element, I compare it with every other element.

**Brute Force Pseudocode:**
```
for i from 0 to n-1:
    for j from i+1 to n-1:
        if nums[i] + nums[j] == target:
            return [i, j]
```

**Complexity Analysis:**
- Time: O(n²) — for 10,000 elements, that's up to 100,000,000 comparisons
- Space: O(1) — we use no extra memory

**Why is this too slow?**
For n = 10,000 (common in interviews), O(n²) means ~50 million operations. At ~10⁸ operations per second, that's 0.5 seconds — right at the time limit edge. For n = 100,000, it's 5 billion operations — a guaranteed timeout.

---

## Discovering the Optimization

Ask yourself: **"What work am I repeating?"**

In the brute force, for each element `nums[i]`, you scan the *entire rest of the array* looking for `target - nums[i]`. You're doing a linear search for the same complement over and over.

**The key observation:**
> *"Instead of searching for the complement, what if I could check for it instantly?"*

Here's the evolution of thought:

**Step 1:** For each element `x`, I need to know: "Does `target - x` exist in the array?"

**Step 2:** Checking "does this exist?" is exactly what a hash map is great at — O(1) lookup!

**Step 3:** But I also need the *index* of where I found it. So I'll store `{value: index}` in the map.

**Step 4:** Now the question becomes: as I scan left to right, for each new element, I ask "is the complement already in my map?" If yes, I found my answer. If no, I add the current element to the map for future elements to check against.

**The insight crystallized:**
> *"Build the answer as you scan. Use the map to answer 'have I seen the complement yet?' in O(1)."*

---

## Visual Walkthrough

**Input:** `nums = [2, 7, 11, 15]`, `target = 9`

```
Start: seen = {}

i=0, num=2
  complement = 9 - 2 = 7
  Is 7 in seen? NO
  Add {2: 0} to seen
  seen = {2: 0}

i=1, num=7
  complement = 9 - 7 = 2
  Is 2 in seen? YES! Found at index 0
  Return [seen[2], 1] = [0, 1]
```

**Answer: [0, 1]** ✓

---

**Now let's walk through the Anagram problem:**

*Given two strings s and t, return true if t is an anagram of s.*

```
s = "anagram", t = "nagaram"

Approach: Count character frequencies in both. If counts match, they're anagrams.

Count s:
a: 3, n: 1, g: 1, r: 1, m: 1

Count t:
n: 1, a: 3, g: 1, r: 1, m: 1

Compare: identical → True ✓
```

---

## Final Optimal Solution

### Two Sum
```python
def two_sum(nums: list[int], target: int) -> list[int]:
    # Maps value -> index for O(1) complement lookup
    seen = {}

    for i, num in enumerate(nums):
        complement = target - num

        # If complement already seen, we found our pair
        if complement in seen:
            return [seen[complement], i]

        # Otherwise, record this number and index for future lookups
        seen[num] = i

    return []   # No solution found (problem guarantees one exists)
```

### Valid Anagram
```python
def is_anagram(s: str, t: str) -> bool:
    # Quick shortcut: different lengths can't be anagrams
    if len(s) != len(t):
        return False

    # Count frequency of each character in both strings
    count = {}

    for ch in s:
        count[ch] = count.get(ch, 0) + 1   # Increment count for s

    for ch in t:
        count[ch] = count.get(ch, 0) - 1   # Decrement count for t

    # If anagram, all counts should cancel to zero
    return all(v == 0 for v in count.values())

# Cleaner version using Counter:
from collections import Counter
def is_anagram_v2(s: str, t: str) -> bool:
    return Counter(s) == Counter(t)
```

### Contains Duplicate
```python
def contains_duplicate(nums: list[int]) -> bool:
    seen = set()
    for num in nums:
        if num in seen:
            return True   # Saw it before!
        seen.add(num)
    return False

# One-liner version:
def contains_duplicate_v2(nums: list[int]) -> bool:
    return len(nums) != len(set(nums))
```

---

## Complexity Breakdown

### Two Sum

| Operation | Time | Space | Why |
|-----------|------|-------|-----|
| Single loop through n elements | O(n) | — | Visit each element once |
| HashMap lookup/insert | O(1) avg | — | Hash function gives direct access |
| HashMap storage | — | O(n) | Store up to n elements |
| **Overall** | **O(n)** | **O(n)** | Linear time, linear space |

**Why O(n) and not O(1) space?** In the worst case (answer is the last two elements), we store every element in the map before finding the answer.

### Valid Anagram

| Operation | Time | Space |
|-----------|------|-------|
| Build frequency map | O(n) | O(1)* |
| Compare maps | O(1)* | — |
| **Overall** | **O(n)** | **O(1)** |

*\*O(1) space because there are only 26 letters — constant alphabet size, not dependent on input size.*

---

## Common Mistakes

### 1. Using the wrong index
```python
# WRONG: returns value, not index
if complement in seen:
    return [seen[complement], num]  # 'num' is a value, not an index!

# CORRECT:
if complement in seen:
    return [seen[complement], i]    # 'i' is the current index
```

### 2. Not handling duplicates properly in Two Sum
```python
# WRONG: adds element before checking
seen[num] = i
if complement in seen:  # This might match num with itself!
    ...

# CORRECT: check FIRST, then add
if complement in seen:
    return [seen[complement], i]
seen[num] = i
```

### 3. Forgetting that `dict.get()` vs `dict[]`
```python
d = {}
d["key"]           # KeyError if "key" doesn't exist!
d.get("key", 0)    # Returns 0 safely if "key" doesn't exist ✓
```

### 4. The 2D array aliasing trap
```python
# WRONG — all rows point to the same list!
grid = [[0] * 3] * 3
grid[0][0] = 1
# grid = [[1,0,0],[1,0,0],[1,0,0]] — all changed!

# CORRECT — each row is independent
grid = [[0] * 3 for _ in range(3)]
grid[0][0] = 1
# grid = [[1,0,0],[0,0,0],[0,0,0]] ✓
```

### 5. Confusing `in` for lists vs. dicts/sets
```python
# List: O(n) — scans entire list
3 in [1, 2, 3, 4, 5]  # Slow for large lists

# Set/Dict: O(1) — instant hash lookup
3 in {1, 2, 3, 4, 5}  # Fast, always
```

---

## Pattern Template

```python
# FREQUENCY COUNT TEMPLATE
def frequency_pattern(data):
    freq = {}
    for item in data:
        freq[item] = freq.get(item, 0) + 1
    return freq

# COMPLEMENT SEARCH TEMPLATE (Two Sum style)
def complement_search(nums, target):
    seen = {}   # {value: index}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
    return []

# GROUPING TEMPLATE (Group Anagrams style)
def grouping_pattern(items):
    groups = {}   # {key: [items with that key]}
    for item in items:
        key = compute_key(item)   # e.g., sorted(item) for anagrams
        if key not in groups:
            groups[key] = []
        groups[key].append(item)
    return list(groups.values())
```

---
