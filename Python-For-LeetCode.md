# Python For LeetCode — Complete Cheat Sheet

> **Who this is for:** College students preparing for technical interviews who know basic Python but need to sharpen their problem-solving toolkit.
>
> **How to use this:** Don't memorize everything at once. Read it, bookmark it, and return to each section as you encounter new problem types. The goal is *recognition* — when you see a problem, you should instantly know which tools to reach for.

---

## Table of Contents

1. [Python Basics](#1-python-basics)
2. [Common Interview Syntax](#2-common-interview-syntax)
3. [Arrays / Lists](#3-arrays--lists)
4. [Hash Maps / Dictionaries](#4-hash-maps--dictionaries)
5. [Sets](#5-sets)
6. [Stacks](#6-stacks)
7. [Queues](#7-queues)
8. [Heaps](#8-heaps)
9. [Linked List Classes](#9-linked-list-classes)
10. [Trees](#10-trees)
11. [Graph Representations](#11-graph-representations)
12. [Time Complexity Cheat Sheet](#12-time-complexity-cheat-sheet)
13. [Space Complexity Cheat Sheet](#13-space-complexity-cheat-sheet)
14. [Common LeetCode Templates](#14-common-leetcode-templates)

---

## 1. Python Basics

### Variables
```python
# Python is dynamically typed — no need to declare types
x = 5           # int
y = 3.14        # float
name = "Alice"  # str
flag = True     # bool
nothing = None  # NoneType (equivalent to null)

# Multiple assignment (swap trick — extremely useful in interviews!)
a, b = 1, 2
a, b = b, a     # Now a=2, b=1 — no temp variable needed!

# Integer division and modulo
10 // 3    # 3  (floor division)
10 % 3     # 1  (remainder)
2 ** 10    # 1024 (exponentiation)

# Python integers have no overflow — they grow arbitrarily large
# This is a HUGE advantage over Java/C++ in interviews
```

> ⚠️ **Common Mistake:** `10 / 3` gives `3.333...` (float), not `3`. Use `//` for integer division.

---

### Loops

```python
# Standard for loop
for i in range(5):          # 0, 1, 2, 3, 4
    print(i)

for i in range(2, 8):       # 2, 3, 4, 5, 6, 7
    print(i)

for i in range(0, 10, 2):   # 0, 2, 4, 6, 8 (step=2)
    print(i)

# Reverse loop (very common in interviews!)
for i in range(4, -1, -1):  # 4, 3, 2, 1, 0
    print(i)

# Loop over a list
nums = [10, 20, 30]
for num in nums:
    print(num)

# Loop with index AND value (use enumerate — see Section 2)
for i, num in enumerate(nums):
    print(i, num)   # 0 10, 1 20, 2 30

# While loop
i = 0
while i < 5:
    i += 1
```

> 💡 **Interview Tip:** In Python, `for i in range(len(arr))` and `for val in arr` both work. Prefer `enumerate` when you need both the index and the value.

---

### Conditionals

```python
x = 10

if x > 5:
    print("big")
elif x == 5:
    print("five")
else:
    print("small")

# One-liner (ternary)
result = "big" if x > 5 else "small"

# Chained comparisons (Python-exclusive — very clean!)
if 0 < x < 100:
    print("in range")

# Checking None
if x is None:
    print("nothing here")

if x is not None:
    print("something here")

# Truthiness — these all evaluate as False:
# None, 0, "", [], {}, set()
if not []:       # True — empty list is falsy
    print("empty!")
```

---

### Functions

```python
# Basic function
def add(a, b):
    return a + b

# Default parameters
def greet(name, greeting="Hello"):
    return f"{greeting}, {name}!"

# Multiple return values (returns a tuple)
def min_max(nums):
    return min(nums), max(nums)

lo, hi = min_max([3, 1, 4, 1, 5])  # Unpack directly

# *args — variable number of positional arguments
def total(*args):
    return sum(args)

total(1, 2, 3)   # 6

# Type hints (optional but good practice)
def two_sum(nums: list[int], target: int) -> list[int]:
    ...
```

---

### Classes

```python
class Node:
    def __init__(self, val):
        self.val = val
        self.next = None   # Used in Linked Lists

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

# Using a class
node = Node(5)
node.next = Node(10)
```

---

## 2. Common Interview Syntax

### `enumerate()` — Loop with index and value

```python
fruits = ["apple", "banana", "cherry"]

for i, fruit in enumerate(fruits):
    print(i, fruit)
# 0 apple
# 1 banana
# 2 cherry

# Start index at 1
for i, fruit in enumerate(fruits, start=1):
    print(i, fruit)
```

> 💡 **When to use:** Whenever you need both the position and the value. Replaces the ugly `for i in range(len(arr)): val = arr[i]` pattern.

---

### `zip()` — Pair up multiple lists

```python
names = ["Alice", "Bob", "Charlie"]
scores = [95, 87, 92]

for name, score in zip(names, scores):
    print(f"{name}: {score}")

# zip stops at the shortest list
# Convert to list of tuples
pairs = list(zip(names, scores))
# [("Alice", 95), ("Bob", 87), ("Charlie", 92)]

# Unzip (transpose)
a, b = zip(*pairs)
```

> 💡 **Interview Use Case:** Comparing two arrays element-by-element without an index variable.

---

### `sorted()` — Sort anything, return new list

```python
nums = [3, 1, 4, 1, 5, 9]

# Basic sort (ascending)
sorted_nums = sorted(nums)          # [1, 1, 3, 4, 5, 9]

# Descending
sorted_desc = sorted(nums, reverse=True)   # [9, 5, 4, 3, 1, 1]

# Sort by custom key
words = ["banana", "fig", "apple", "kiwi"]
by_length = sorted(words, key=len)   # ["fig", "kiwi", "apple", "banana"]

# Sort list of tuples by second element
pairs = [(1, 3), (2, 1), (3, 2)]
sorted_pairs = sorted(pairs, key=lambda x: x[1])   # [(2,1),(3,2),(1,3)]

# In-place sort (modifies original)
nums.sort()

# Sort strings by custom criteria — anagram grouping trick
# Sort each word's characters
sorted("listen")   # 'eilnst'
sorted("silent")   # 'eilnst'  — same! they're anagrams
```

---

### `lambda` — Anonymous (one-line) functions

```python
# lambda arguments: expression
square = lambda x: x ** 2
square(5)   # 25

# Most common use: as a key for sorted()
intervals = [[1, 3], [2, 6], [0, 4]]
sorted(intervals, key=lambda x: x[0])   # Sort by start time

# Two-key sort
data = [("Alice", 25), ("Bob", 22), ("Charlie", 25)]
sorted(data, key=lambda x: (x[1], x[0]))   # Sort by age, then name
```

---

### List Comprehensions

```python
# [expression for item in iterable if condition]

# Basic
squares = [x**2 for x in range(10)]

# With filter
evens = [x for x in range(20) if x % 2 == 0]

# Transform strings
upper = [s.upper() for s in ["hello", "world"]]

# Flatten 2D list
matrix = [[1,2],[3,4],[5,6]]
flat = [val for row in matrix for val in row]   # [1,2,3,4,5,6]

# 2D grid creation (very useful for graph problems)
rows, cols = 3, 4
grid = [[0] * cols for _ in range(rows)]
# ⚠️ DO NOT use: grid = [[0] * cols] * rows  — all rows share same reference!
```

> ⚠️ **Critical Mistake to Avoid:** `[[0]*4]*3` creates 3 references to the *same* inner list. Modifying one modifies all. Always use the comprehension version.

---

### Dictionary Comprehensions

```python
# {key_expr: val_expr for item in iterable}

# Basic
squares = {x: x**2 for x in range(5)}
# {0:0, 1:1, 2:4, 3:9, 4:16}

# Invert a dictionary
original = {"a": 1, "b": 2, "c": 3}
inverted = {v: k for k, v in original.items()}
# {1:'a', 2:'b', 3:'c'}

# Filter
big_only = {k: v for k, v in original.items() if v > 1}
```

---

## 3. Arrays / Lists

```python
# ── CREATION ──────────────────────────────────────────
arr = []                    # Empty list
arr = [1, 2, 3, 4, 5]      # With values
arr = [0] * 10              # Ten zeros
arr = list(range(5))        # [0, 1, 2, 3, 4]

# ── ACCESS ────────────────────────────────────────────
arr[0]       # First element
arr[-1]      # Last element  ← Use this! Much cleaner than arr[len(arr)-1]
arr[-2]      # Second to last

# Slicing
arr[1:4]     # [2, 3, 4]  (index 1 up to but not including 4)
arr[:3]      # [1, 2, 3]  (from start to index 3)
arr[2:]      # [3, 4, 5]  (from index 2 to end)
arr[::-1]    # [5, 4, 3, 2, 1]  (reversed!)
arr[::2]     # [1, 3, 5]  (every other element)

# ── INSERT ────────────────────────────────────────────
arr.append(6)         # Add to end — O(1)
arr.insert(0, 99)     # Insert at index 0 — O(n) ← slow!
arr.extend([7, 8])    # Add multiple at end — O(k)

# ── DELETE ────────────────────────────────────────────
arr.pop()             # Remove and return last — O(1)
arr.pop(0)            # Remove and return first — O(n) ← slow!
arr.remove(3)         # Remove first occurrence of value 3 — O(n)
del arr[2]            # Delete at index 2 — O(n)

# ── SORTING ───────────────────────────────────────────
arr.sort()            # In-place, O(n log n)
arr.sort(reverse=True)
new = sorted(arr)     # Returns new sorted list, original unchanged

# ── USEFUL TRICKS ─────────────────────────────────────
len(arr)              # Length — O(1)
sum(arr)              # Sum — O(n)
min(arr)              # Minimum — O(n)
max(arr)              # Maximum — O(n)
3 in arr              # Membership test — O(n)
arr.index(3)          # First index of value — O(n)
arr.count(3)          # Count occurrences — O(n)
arr.reverse()         # Reverse in-place — O(n)

# String ↔ List conversions (very common!)
s = "hello"
chars = list(s)              # ['h','e','l','l','o']
"".join(chars)               # "hello"
"hello world".split()        # ["hello", "world"]
"a,b,c".split(",")           # ["a","b","c"]
",".join(["a","b","c"])      # "a,b,c"

# Copy a list (avoid reference bugs)
copy = arr[:]       # Shallow copy
copy = arr.copy()   # Same thing
import copy; deep = copy.deepcopy(arr)   # Deep copy (nested lists)
```

---

## 4. Hash Maps / Dictionaries

> **Core Insight:** Hash maps give us O(1) average lookup, insert, and delete. They are the #1 tool for trading space for speed.

```python
# ── CREATION ──────────────────────────────────────────
d = {}
d = dict()
d = {"a": 1, "b": 2}

# ── LOOKUP ────────────────────────────────────────────
d["a"]              # Returns 1, raises KeyError if missing
d.get("a")          # Returns 1, returns None if missing ← prefer this
d.get("z", 0)       # Returns 0 as default if "z" not found ← very useful!

# Check membership
"a" in d            # True — O(1)
"z" not in d        # True

# ── UPDATE ────────────────────────────────────────────
d["c"] = 3          # Set/overwrite
d["a"] += 1         # Increment existing value

# ── ITERATION ─────────────────────────────────────────
for key in d:           # Iterate over keys
    print(key)

for val in d.values():  # Iterate over values
    print(val)

for key, val in d.items():  # Iterate over key-value pairs
    print(key, val)

# ── DELETE ────────────────────────────────────────────
del d["a"]              # Raises KeyError if missing
d.pop("a", None)        # Safe delete — no error if missing

# ── FREQUENCY COUNTING ────────────────────────────────
# Pattern 1: Manual
nums = [1, 2, 2, 3, 3, 3]
freq = {}
for num in nums:
    freq[num] = freq.get(num, 0) + 1
# {1:1, 2:2, 3:3}

# Pattern 2: collections.Counter (cleaner!)
from collections import Counter
freq = Counter(nums)                 # Counter({3:3, 2:2, 1:1})
freq.most_common(2)                  # [(3,3),(2,2)] — top 2

# Pattern 3: defaultdict (never get KeyError)
from collections import defaultdict
freq = defaultdict(int)              # Default value is int() = 0
for num in nums:
    freq[num] += 1

# defaultdict with list — for grouping
groups = defaultdict(list)
groups["vowels"].append("a")        # No KeyError!
groups["vowels"].append("e")

# ── COMMON INTERVIEW PATTERNS ─────────────────────────
# Check if two strings are anagrams
def is_anagram(s, t):
    return Counter(s) == Counter(t)

# Two Sum lookup pattern
def two_sum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
```

---

## 5. Sets

> **Core Insight:** Sets give O(1) lookup just like hash maps, but without values — just keys. Perfect for membership testing and deduplication.

```python
# ── CREATION ──────────────────────────────────────────
s = set()
s = {1, 2, 3}
s = set([1, 2, 2, 3])    # {1, 2, 3} — duplicates removed!

# ── OPERATIONS ────────────────────────────────────────
s.add(4)            # Add element — O(1)
s.remove(2)         # Remove, raises KeyError if missing
s.discard(2)        # Safe remove — no error if missing
s.pop()             # Remove and return arbitrary element

# ── MEMBERSHIP ────────────────────────────────────────
3 in s              # O(1) ← this is the killer feature vs list's O(n)
3 not in s

# ── SET MATH ──────────────────────────────────────────
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

a | b               # Union:        {1,2,3,4,5,6}
a & b               # Intersection: {3,4}
a - b               # Difference:   {1,2}  (in a but not b)
a ^ b               # Symmetric diff: {1,2,5,6}  (in one but not both)

# ── COMMON PATTERNS ───────────────────────────────────
# Deduplicate a list
unique = list(set([1,2,2,3,3,3]))   # [1,2,3]

# Convert string to set of unique characters
chars = set("hello")    # {'h','e','l','o'}

# Visited tracking in graph/BFS/DFS
visited = set()
visited.add(node)
if node not in visited:
    ...

# Cycle detection
seen = set()
while node:
    if node in seen:
        return True   # Cycle found!
    seen.add(node)
    node = node.next
```

---

## 6. Stacks

> **Mental Model:** A stack of plates. You can only add or remove from the top. **Last In, First Out (LIFO).**

```python
# Python lists make perfect stacks!
stack = []

# Push
stack.append(1)
stack.append(2)
stack.append(3)
# stack = [1, 2, 3]

# Pop (from top)
top = stack.pop()   # Returns 3, stack = [1, 2]

# Peek (look at top without removing)
top = stack[-1]     # 2, stack unchanged

# Check if empty
if not stack:
    print("empty")

if len(stack) == 0:
    print("empty")

# ── COMMON PATTERNS ───────────────────────────────────
# Balanced parentheses check
def is_valid(s):
    stack = []
    pairs = {')': '(', '}': '{', ']': '['}
    for ch in s:
        if ch in "({[":
            stack.append(ch)
        elif ch in pairs:
            if not stack or stack[-1] != pairs[ch]:
                return False
            stack.pop()
    return not stack   # Valid only if stack is empty at end

# Monotonic stack (next greater element)
def next_greater(nums):
    result = [-1] * len(nums)
    stack = []   # Stores indices
    for i, num in enumerate(nums):
        while stack and nums[stack[-1]] < num:
            idx = stack.pop()
            result[idx] = num
        stack.append(i)
    return result
```

---

## 7. Queues

> **Mental Model:** A line at a coffee shop. First person in line gets served first. **First In, First Out (FIFO).**

```python
from collections import deque

# ── CREATION ──────────────────────────────────────────
q = deque()
q = deque([1, 2, 3])

# ── OPERATIONS ────────────────────────────────────────
q.append(4)         # Add to right (back of queue) — O(1)
q.appendleft(0)     # Add to left (front of queue) — O(1)

q.pop()             # Remove from right — O(1)
q.popleft()         # Remove from left (dequeue) — O(1)

q[0]                # Peek front — O(1)
q[-1]               # Peek back — O(1)

len(q)              # Size
if not q:           # Check empty
    ...

# ⚠️ Why not just use a list as a queue?
# list.pop(0) is O(n) because it shifts every element
# deque.popleft() is O(1) — always use deque for queues!

# ── BFS TEMPLATE ──────────────────────────────────────
from collections import deque

def bfs(graph, start):
    visited = set([start])
    queue = deque([start])

    while queue:
        node = queue.popleft()
        # Process node
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
```

---

## 8. Heaps

> **Mental Model:** A heap is a "smart list" that always gives you the smallest (or largest) element in O(log n) time.

```python
import heapq

# ── MIN HEAP (default) ────────────────────────────────
heap = []
heapq.heappush(heap, 3)
heapq.heappush(heap, 1)
heapq.heappush(heap, 4)
heapq.heappush(heap, 1)
heapq.heappush(heap, 5)

heapq.heappop(heap)     # Returns 1 (smallest)
heap[0]                 # Peek at smallest without removing

# Heapify an existing list — O(n)
nums = [3, 1, 4, 1, 5, 9]
heapq.heapify(nums)     # Converts in-place to min heap

# ── MAX HEAP (Python trick) ────────────────────────────
# Python only has min heap. To simulate max heap, negate values!
max_heap = []
heapq.heappush(max_heap, -3)
heapq.heappush(max_heap, -1)
heapq.heappush(max_heap, -4)

largest = -heapq.heappop(max_heap)   # Negate back to get 4

# ── HEAP WITH TUPLES ──────────────────────────────────
# Push (priority, value) tuples — sorted by first element
heap = []
heapq.heappush(heap, (2, "task B"))
heapq.heappush(heap, (1, "task A"))
heapq.heappush(heap, (3, "task C"))

priority, task = heapq.heappop(heap)   # (1, "task A")

# ── COMMON PATTERNS ───────────────────────────────────
# K largest elements
def k_largest(nums, k):
    return heapq.nlargest(k, nums)

# K smallest elements
def k_smallest(nums, k):
    return heapq.nsmallest(k, nums)

# K most frequent elements
from collections import Counter
def top_k_frequent(nums, k):
    count = Counter(nums)
    heap = []
    for num, freq in count.items():
        heapq.heappush(heap, (freq, num))
        if len(heap) > k:
            heapq.heappop(heap)
    return [num for freq, num in heap]
```

---

## 9. Linked List Classes

```python
# ── SINGLY LINKED LIST ────────────────────────────────
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

# Build: 1 -> 2 -> 3 -> None
head = ListNode(1)
head.next = ListNode(2)
head.next.next = ListNode(3)

# Traverse
current = head
while current:
    print(current.val)
    current = current.next

# ── DUMMY HEAD TRICK ──────────────────────────────────
# Use a dummy node to simplify edge cases (empty list, insert at head)
dummy = ListNode(0)
dummy.next = head
# Work with dummy.next instead of head
# Return dummy.next at the end

# ── FAST & SLOW POINTERS ──────────────────────────────
# Find middle of linked list
def find_middle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow   # slow is at middle

# Detect cycle
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True
    return False

# ── DOUBLY LINKED LIST ────────────────────────────────
class DListNode:
    def __init__(self, val=0):
        self.val = val
        self.prev = None
        self.next = None
```

---

## 10. Trees

```python
# ── TREE NODE ────────────────────────────────────────
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

# ── TREE TRAVERSALS ───────────────────────────────────

# Inorder: Left → Root → Right  (gives sorted order for BST!)
def inorder(root):
    if not root:
        return
    inorder(root.left)
    print(root.val)
    inorder(root.right)

# Preorder: Root → Left → Right  (good for copying/serializing tree)
def preorder(root):
    if not root:
        return
    print(root.val)
    preorder(root.left)
    preorder(root.right)

# Postorder: Left → Right → Root  (good for deletion)
def postorder(root):
    if not root:
        return
    postorder(root.left)
    postorder(root.right)
    print(root.val)

# Level Order (BFS)
from collections import deque
def level_order(root):
    if not root:
        return []
    result = []
    queue = deque([root])
    while queue:
        level = []
        for _ in range(len(queue)):   # Process entire level
            node = queue.popleft()
            level.append(node.val)
            if node.left:  queue.append(node.left)
            if node.right: queue.append(node.right)
        result.append(level)
    return result

# ── COMMON RECURSIVE PATTERNS ─────────────────────────
# Height of tree
def height(root):
    if not root:
        return 0
    return 1 + max(height(root.left), height(root.right))

# Count nodes
def count(root):
    if not root:
        return 0
    return 1 + count(root.left) + count(root.right)
```

---

## 11. Graph Representations

```python
# ── ADJACENCY LIST (most common for LeetCode) ─────────
# Graph: 0-1, 0-2, 1-3, 2-3

# From edge list
edges = [[0,1],[0,2],[1,3],[2,3]]
graph = defaultdict(list)
for u, v in edges:
    graph[u].append(v)
    graph[v].append(u)   # For undirected graph

# Manual
graph = {
    0: [1, 2],
    1: [0, 3],
    2: [0, 3],
    3: [1, 2]
}

# ── ADJACENCY MATRIX ──────────────────────────────────
n = 4
matrix = [[0]*n for _ in range(n)]
matrix[0][1] = 1   # Edge from 0 to 1
matrix[1][0] = 1   # Undirected

# ── DFS (Recursive) ───────────────────────────────────
def dfs(graph, node, visited):
    visited.add(node)
    for neighbor in graph[node]:
        if neighbor not in visited:
            dfs(graph, neighbor, visited)

# ── DFS (Iterative with stack) ────────────────────────
def dfs_iter(graph, start):
    visited = set()
    stack = [start]
    while stack:
        node = stack.pop()
        if node in visited:
            continue
        visited.add(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                stack.append(neighbor)

# ── BFS ───────────────────────────────────────────────
def bfs(graph, start):
    visited = set([start])
    queue = deque([start])
    while queue:
        node = queue.popleft()
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)

# ── GRID GRAPH (2D array as graph) ────────────────────
# Four directions: up, down, left, right
directions = [(-1,0),(1,0),(0,-1),(0,1)]

def valid(row, col, rows, cols):
    return 0 <= row < rows and 0 <= col < cols

def grid_bfs(grid, start_r, start_c):
    rows, cols = len(grid), len(grid[0])
    visited = set([(start_r, start_c)])
    queue = deque([(start_r, start_c)])
    while queue:
        r, c = queue.popleft()
        for dr, dc in directions:
            nr, nc = r+dr, c+dc
            if valid(nr, nc, rows, cols) and (nr,nc) not in visited:
                visited.add((nr, nc))
                queue.append((nr, nc))
```

---

## 12. Time Complexity Cheat Sheet

| Complexity | Name | Example |
|------------|------|---------|
| O(1) | Constant | Dictionary lookup, array access by index |
| O(log n) | Logarithmic | Binary search, heap push/pop |
| O(n) | Linear | Single loop, linear search |
| O(n log n) | Linearithmic | Merge sort, heap sort, Python's `sort()` |
| O(n²) | Quadratic | Nested loops, bubble sort |
| O(2ⁿ) | Exponential | Recursive Fibonacci, subset generation |
| O(n!) | Factorial | Permutation generation |

### Operations by Data Structure

| Structure | Access | Search | Insert | Delete |
|-----------|--------|--------|--------|--------|
| Array | O(1) | O(n) | O(n) | O(n) |
| HashMap | O(1) | O(1) | O(1) | O(1) |
| Set | — | O(1) | O(1) | O(1) |
| Stack/Queue | O(1) top | O(n) | O(1) | O(1) |
| Heap | O(1) peek | O(n) | O(log n) | O(log n) |
| BST (balanced) | — | O(log n) | O(log n) | O(log n) |

### Sorting Algorithms

| Algorithm | Best | Average | Worst | Stable? |
|-----------|------|---------|-------|---------|
| Python sort (Timsort) | O(n) | O(n log n) | O(n log n) | ✅ Yes |
| Merge Sort | O(n log n) | O(n log n) | O(n log n) | ✅ Yes |
| Quick Sort | O(n log n) | O(n log n) | O(n²) | ❌ No |
| Heap Sort | O(n log n) | O(n log n) | O(n log n) | ❌ No |
| Counting Sort | O(n+k) | O(n+k) | O(n+k) | ✅ Yes |

### Interview Complexity Rules of Thumb
- If you see a **loop inside a loop** → O(n²) or worse
- If you see **binary search** → O(log n)
- If you see **recursion that halves input** → O(log n)
- If you see **recursion with 2 branches** → O(2ⁿ) (unless memoized!)
- If you use **HashMap/Set** → usually drops O(n²) → O(n)

---

## 13. Space Complexity Cheat Sheet

| Space | Explanation | Example |
|-------|-------------|---------|
| O(1) | No extra space | Two pointers on sorted array |
| O(n) | Linear extra space | HashMap storing n elements |
| O(n²) | Quadratic extra space | 2D DP table |
| O(log n) | Call stack depth of binary search recursion | DFS on balanced tree |
| O(n) | Call stack depth of linear recursion | DFS on a path graph |
| O(h) | Tree height (can be log n to n) | DFS traversal |

### Key Rules
- **Recursion** uses call stack space = O(depth of recursion)
- **HashMap** uses O(n) where n = number of entries
- **2D grid/DP table** uses O(rows × cols)
- **Sorting** in Python uses O(n) for Timsort (not in-place like heap sort)

---

## 14. Common LeetCode Templates

### Template 1: Two Pointers (Sorted Array)
```python
def two_pointers(nums, target):
    left, right = 0, len(nums) - 1
    while left < right:
        current = nums[left] + nums[right]
        if current == target:
            return [left, right]
        elif current < target:
            left += 1
        else:
            right -= 1
    return []
```

### Template 2: Sliding Window (Variable Size)
```python
def sliding_window(nums, k):
    left = 0
    window_state = ...   # e.g., sum, count, set
    result = 0

    for right in range(len(nums)):
        # Expand window: add nums[right]
        ...

        # Shrink window: while invalid, remove nums[left]
        while window_is_invalid:
            # Remove nums[left] from window_state
            left += 1

        # Update result
        result = max(result, right - left + 1)

    return result
```

### Template 3: Binary Search
```python
def binary_search(nums, target):
    left, right = 0, len(nums) - 1
    while left <= right:
        mid = left + (right - left) // 2   # Avoids overflow
        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1
```

### Template 4: DFS on Tree
```python
def dfs(root):
    if not root:                    # Base case
        return base_value
    left_result = dfs(root.left)    # Recurse left
    right_result = dfs(root.right)  # Recurse right
    return combine(left_result, right_result, root.val)
```

### Template 5: BFS (Level Order)
```python
from collections import deque
def bfs(root):
    if not root: return []
    queue = deque([root])
    result = []
    while queue:
        for _ in range(len(queue)):   # Snapshot current level size!
            node = queue.popleft()
            result.append(node.val)
            if node.left:  queue.append(node.left)
            if node.right: queue.append(node.right)
    return result
```

### Template 6: Dynamic Programming (1D)
```python
def dp(nums):
    n = len(nums)
    dp = [0] * n       # Or float('inf') / float('-inf')
    dp[0] = base_case

    for i in range(1, n):
        dp[i] = f(dp[i-1], nums[i])   # Recurrence relation

    return dp[n-1]
```

### Template 7: Backtracking
```python
def backtrack(start, path, result):
    if is_complete(path):
        result.append(path[:])   # Copy! Don't append reference
        return

    for choice in get_choices(start):
        if is_valid(choice):
            path.append(choice)        # Choose
            backtrack(next_start, path, result)
            path.pop()                 # Unchoose (backtrack)
```

### Template 8: Graph DFS (Iterative)
```python
def dfs(graph, start):
    visited = set()
    stack = [start]
    while stack:
        node = stack.pop()
        if node in visited:
            continue
        visited.add(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                stack.append(neighbor)
```

### Template 9: Union Find (Disjoint Set)
```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # Path compression
        return self.parent[x]

    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py: return False
        if self.rank[px] < self.rank[py]:
            px, py = py, px
        self.parent[py] = px
        if self.rank[px] == self.rank[py]:
            self.rank[px] += 1
        return True
```

### Template 10: Trie
```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for ch in word:
            if ch not in node.children:
                node.children[ch] = TrieNode()
            node = node.children[ch]
        node.is_end = True

    def search(self, word):
        node = self.root
        for ch in word:
            if ch not in node.children:
                return False
            node = node.children[ch]
        return node.is_end
```

---

> 📌 **Final Reminder:** Syntax is the easy part. The hard part — and the part that actually matters in interviews — is *knowing when to use what*. For each problem, ask yourself:
>
> 1. What is the input? What am I returning?
> 2. What constraints exist? (sorted? unique? within a range?)
> 3. Which data structure gives me the operation I need in O(1) or O(log n)?
> 4. What's the simplest brute force? How can I optimize it?
>
> Practice building this thinking process, not memorizing code. The code will follow naturally.
