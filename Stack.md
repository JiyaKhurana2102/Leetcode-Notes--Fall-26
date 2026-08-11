# Stack

## Why This Topic Matters

Stacks are everywhere in computer science — your browser's back button, your editor's undo function, how programming languages execute function calls. Interviewers love stack problems because they test whether you can recognize **nested structure** and **deferred decisions**.

**Real-world applications:**
- Function call stack (how Python itself executes your code)
- Browser history (back/forward navigation)
- Undo/redo in text editors
- Syntax highlighting and parsing (compilers, IDEs)
- Expression evaluation (calculators)

**Interview frequency:** High. Appears in ~25% of problems. Stack problems are often disguised — they don't always say "use a stack." You have to recognize when processing something now would require information about what came before.

---

## The Core Idea

Imagine a stack of plates in a cafeteria. You can only:
1. **Push** a plate onto the top
2. **Pop** a plate off the top
3. **Peek** at what's on top without removing it

You can't reach into the middle. The last plate put on is the first one taken off: **Last In, First Out (LIFO)**.

> *A stack is the perfect structure when you need to "remember where you came from" or "defer a decision until later context arrives."*

**Mental models for when stacks appear:**
- **Matching:** You open a parenthesis. You don't know if it's valid yet. Push it. When you see a closing parenthesis, pop and check if they match.
- **Next Greater Element:** You see `3`, but you don't know if 3 is the "answer" yet — maybe the next element is bigger. Push 3. When you see a bigger element, the stack tells you who was waiting for an answer.
- **Undo:** You perform action A, then B, then C. To undo, you want C reversed first (LIFO).

---

## Pattern Recognition

### What clues should make you think of a Stack?

✅ **Positive indicators:**
- The problem involves **matching pairs**: brackets, parentheses, tags
- You're told to find the **next greater/smaller element**
- The problem has **nested structure**: `((()))`, `3 * (2 + 4)`
- You need to **undo** or **backtrack** decisions in reverse order
- The problem involves **temperatures/prices/buildings** and "the next day when..."
- Keywords: "valid parentheses," "decode string," "evaluate expression," "asteroid collision"

❌ **When a Stack is NOT appropriate:**
- You need to access elements in the order they were inserted (use a Queue/deque)
- You need random access to the middle (use an array)
- The problem requires forward knowledge without revisiting (use greedy/DP)

---

## Brute Force Thinking

### Example Problem: Valid Parentheses
*Given a string of `()[]{}`, return true if it's valid (every open has a matching close in the right order).*

**Naive approach:**
Repeatedly scan the string, removing adjacent matched pairs until no more can be removed.

```
while "()" in s or "[]" in s or "{}" in s:
    s = s.replace("()", "")
    s = s.replace("[]", "")
    s = s.replace("{}", "")
return s == ""
```

**Complexity:**
- Time: O(n²) — each removal pass is O(n), and we do O(n) passes
- Space: O(n) — string copies

**Why it's awkward:** Creating new strings in each pass is wasteful. And for deeply nested strings like `(((())))`, we'd do O(n) passes each of O(n) length.

---

### Example Problem: Daily Temperatures
*Given daily temperatures, return an array where `result[i]` = number of days until a warmer temperature.*

**Naive approach:**
For each day, scan forward until you find a warmer day.

```
result = [0] * n
for i from 0 to n-1:
    for j from i+1 to n-1:
        if temps[j] > temps[i]:
            result[i] = j - i
            break
return result
```

**Complexity:** O(n²) — for each of n days, scan up to n future days.

**The key inefficiency:** When we're at day `i`, we scan ALL future days. But when we move to day `i+1`, we scan again — many of those same future days we just scanned!

---

## Discovering the Optimization

### Valid Parentheses: The "Remember What We Opened" Insight

**Ask yourself:** "When I see a closing bracket, what do I need to know?"
Answer: The *most recent unclosed opening bracket*. Not the first one — the most recent one.

"Most recent" = top of a stack.

**Evolution of thought:**
1. Scan left to right.
2. Open brackets → we don't know yet if they'll match. **Push them.**
3. Close bracket → does it match the most recent open bracket? **Pop and compare.**
4. If ever they don't match, or the stack is empty when we need to pop → invalid.
5. At the end: valid only if the stack is empty (every open was matched).

---

### Daily Temperatures: The Monotonic Stack Insight

**The key observation:** For temperature `T[i]`, we want to find the *next* day `j > i` where `T[j] > T[i]`.

**Insight:** Temperatures waiting for their "next warmer day" can be put on a stack. When a warm day arrives, it can "answer" all the temperatures on the stack that are colder than it.

**Evolution:**
1. Maintain a stack of *indices of temperatures waiting for a warmer day*.
2. For each new temperature `T[right]`:
   - While the stack is non-empty AND `T[right] > T[stack[-1]]`:
     - Pop the index `idx` — we found the answer for `idx`!
     - `result[idx] = right - idx`
   - Push `right` onto the stack (it's now waiting)
3. Any indices left in the stack never found a warmer day → answer remains 0.

This is called a **Monotonic Stack** — we maintain the stack in decreasing (or increasing) order of values.

---

## Visual Walkthrough

### Valid Parentheses

```
Input: s = "({[]})"

Step 1: char='('  → open bracket, push
  stack = ['(']

Step 2: char='{'  → open bracket, push
  stack = ['(', '{']

Step 3: char='['  → open bracket, push
  stack = ['(', '{', '[']

Step 4: char=']'  → closing bracket
  Top of stack = '[' → matches ']'? YES
  Pop '['
  stack = ['(', '{']

Step 5: char='}'  → closing bracket
  Top of stack = '{' → matches '}'? YES
  Pop '{'
  stack = ['(']

Step 6: char=')'  → closing bracket
  Top of stack = '(' → matches ')'? YES
  Pop '('
  stack = []

End: stack is empty → VALID ✓
```

---

### Daily Temperatures (Monotonic Stack)

```
temps = [73, 74, 75, 71, 69, 72, 76, 73]
result = [ 0,  0,  0,  0,  0,  0,  0,  0]

i=0: T=73, stack empty, push 0
  stack = [0]  (index 0, temp 73)

i=1: T=74 > T[stack[-1]]=73
  Pop 0: result[0] = 1-0 = 1
  stack empty, push 1
  stack = [1]  (index 1, temp 74)

i=2: T=75 > T[stack[-1]]=74
  Pop 1: result[1] = 2-1 = 1
  stack empty, push 2
  stack = [2]  (index 2, temp 75)

i=3: T=71 < T[2]=75, push 3
  stack = [2, 3]

i=4: T=69 < T[3]=71, push 4
  stack = [2, 3, 4]

i=5: T=72 > T[4]=69
  Pop 4: result[4] = 5-4 = 1
  T=72 > T[3]=71
  Pop 3: result[3] = 5-3 = 2
  T=72 < T[2]=75, push 5
  stack = [2, 5]

i=6: T=76 > T[5]=72
  Pop 5: result[5] = 6-5 = 1
  T=76 > T[2]=75
  Pop 2: result[2] = 6-2 = 4
  stack empty, push 6
  stack = [6]

i=7: T=73 < T[6]=76, push 7
  stack = [6, 7]  ← these never found warmer days

result = [1, 1, 4, 2, 1, 1, 0, 0]
```

---

## Final Optimal Solution

### Valid Parentheses
```python
def is_valid(s: str) -> bool:
    stack = []
    # Map closing bracket to its expected opening bracket
    matching = {')': '(', '}': '{', ']': '['}

    for char in s:
        if char in "({[":
            stack.append(char)        # Open bracket: push and wait
        else:
            # Close bracket: must match the most recent open bracket
            if not stack or stack[-1] != matching[char]:
                return False
            stack.pop()

    # Valid only if every opened bracket was closed
    return len(stack) == 0
```

### Daily Temperatures
```python
def daily_temperatures(temperatures: list[int]) -> list[int]:
    result = [0] * len(temperatures)
    stack = []   # Stores indices of "waiting" temperatures

    for i, temp in enumerate(temperatures):
        # This temperature answers the question for all colder temps on stack
        while stack and temperatures[stack[-1]] < temp:
            prev_idx = stack.pop()
            result[prev_idx] = i - prev_idx

        stack.append(i)   # Current temp is now "waiting" for its answer

    return result   # Remaining items in stack stay 0 (never found warmer)
```

### Min Stack (Design Problem)
```python
class MinStack:
    """Stack that supports push, pop, top, and getMin in O(1)."""

    def __init__(self):
        self.stack = []       # Main stack: stores all values
        self.min_stack = []   # Auxiliary stack: tracks minimums

    def push(self, val: int) -> None:
        self.stack.append(val)
        # Push new minimum: either val is new min, or min stays the same
        current_min = min(val, self.min_stack[-1] if self.min_stack else val)
        self.min_stack.append(current_min)

    def pop(self) -> None:
        self.stack.pop()
        self.min_stack.pop()   # Keep in sync!

    def top(self) -> int:
        return self.stack[-1]

    def getMin(self) -> int:
        return self.min_stack[-1]   # Always O(1)!
```

---

## Complexity Breakdown

### Valid Parentheses

| Operation | Time | Space | Why |
|-----------|------|-------|-----|
| Single scan | O(n) | — | Visit each character once |
| Stack push/pop | O(1) each | O(n) worst | Stack holds at most n/2 open brackets |
| **Overall** | **O(n)** | **O(n)** | |

### Daily Temperatures (Monotonic Stack)

| Operation | Time | Space | Why |
|-----------|------|-------|-----|
| Outer loop | O(n) | — | Visit each index once |
| Stack pops (total) | O(n) amortized | — | Each index pushed/popped exactly once |
| Stack storage | — | O(n) | Worst case: all in decreasing order |
| **Overall** | **O(n)** | **O(n)** | |

**The amortized argument for O(n):** Even though there's a `while` loop inside the `for` loop, each index is pushed onto the stack exactly once and popped at most once. Total operations = O(2n) = O(n).

---

## Common Mistakes

### 1. Popping from an empty stack
```python
# WRONG: KeyError/IndexError if stack is empty
stack[-1]   # Crashes if stack is empty

# CORRECT: always check first
if stack:
    top = stack[-1]
# or
if not stack or stack[-1] != expected:
    return False
```

### 2. Forgetting that Python lists use append/pop (not push/pop)
```python
# There's no stack.push() in Python!
stack.append(x)   # Push ✓
stack.pop()       # Pop from top ✓
stack[-1]         # Peek ✓
```

### 3. Not checking stack emptiness at the end
```python
# Valid Parentheses: "((" should return False
# WRONG: forgets to check if unclosed brackets remain
return True   # Always returns True!

# CORRECT:
return len(stack) == 0
```

### 4. Min Stack: Forgetting to keep min_stack in sync
```python
# Every push must push to BOTH stacks
# Every pop must pop from BOTH stacks
# They must always have the same length
```

### 5. Monotonic stack direction confusion
```python
# For "next GREATER element": maintain DECREASING stack
# (pop when new element is GREATER than top)

# For "next SMALLER element": maintain INCREASING stack
# (pop when new element is SMALLER than top)
```

---

## Pattern Template

```python
# TEMPLATE 1: Matching brackets / balanced structure
def matching_template(s):
    stack = []
    for char in s:
        if is_opening(char):
            stack.append(char)
        else:
            if not stack or not matches(stack[-1], char):
                return False
            stack.pop()
    return len(stack) == 0

# TEMPLATE 2: Monotonic decreasing stack (next greater element)
def next_greater_template(nums):
    n = len(nums)
    result = [-1] * n
    stack = []   # Indices waiting for their "next greater"

    for i in range(n):
        while stack and nums[stack[-1]] < nums[i]:
            idx = stack.pop()
            result[idx] = nums[i]   # nums[i] is the next greater for idx
        stack.append(i)

    return result

# TEMPLATE 3: Monotonic increasing stack (next smaller element)
def next_smaller_template(nums):
    n = len(nums)
    result = [-1] * n
    stack = []

    for i in range(n):
        while stack and nums[stack[-1]] > nums[i]:
            idx = stack.pop()
            result[idx] = nums[i]
        stack.append(i)

    return result
```
---

## Practice Problems

### 🟢 Easy: Valid Parentheses (LeetCode #20)
**Why this problem:** The quintessential stack problem. If students understand this, they understand the stack pattern. Forces students to see why LIFO is exactly the right structure for nested matching.

**Concept reinforced:** Stack for matching; checking emptiness; deferred matching.

---

### 🟡 Medium: Daily Temperatures (LeetCode #739)
**Why this problem:** Introduces the Monotonic Stack pattern. Students must make the leap from "what information do I need?" to "store indices on the stack." Very common interview problem at mid-level companies.

**Concept reinforced:** Monotonic decreasing stack; storing indices not values; amortized O(n).

---

### 🟠 Medium/Hard: Largest Rectangle in Histogram (LeetCode #84)
**Why this problem:** One of the hardest stack problems. Requires using a monotonic stack to find, for each bar, the first smaller bar to its left and right. The solution is non-obvious and frequently appears at top companies (Google, Facebook). Solving this means you've truly mastered monotonic stacks.

**Concept reinforced:** Monotonic stack for range problems; "area bounded by the shortest bar" insight.

---
