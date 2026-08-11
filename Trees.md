# Trees

## Why This Topic Matters

Trees are the data structure that secretly powers most of the software you use. Every file system, every database, every parsed expression is a tree. Interviewers love tree problems because they test **recursive thinking** — the ability to break a problem into identical subproblems — which is a fundamental programming skill.

**Real-world applications:**
- File systems (folders containing folders)
- HTML/XML DOM (nested tags)
- Database B-trees (sorted storage and lookup)
- Compiler abstract syntax trees (parsing code)
- Decision trees in machine learning

**Interview frequency:** Very high. ~30% of all LeetCode problems involve trees or graphs. Once you master tree recursion, many "hard" problems become much more approachable.

---

## The Core Idea

A tree is a **recursive data structure** — each tree is either:
- Empty (`None`), OR
- A root node + a left subtree + a right subtree

Notice how the definition refers to itself? That's exactly why **recursion** is the natural tool for trees.

> *The pattern for almost every tree problem is: solve it for the left subtree, solve it for the right subtree, combine the results with the root.*

**The magic of tree recursion:**
When you write a function that calls itself on `root.left` and `root.right`, you're not writing "scan the left side" — you're writing "trust that this same function will solve the left side for you." Each recursive call handles a smaller tree.

**The mental model:**
Imagine you're at the top of a corporate hierarchy. You want to know: "How many employees are in my company?"
- You ask your left VP: "How many in your division?" (recursive call)
- You ask your right VP: "How many in your division?" (recursive call)
- They each ask their direct reports, who ask theirs...
- Everyone answers up the chain
- You add: `left + right + 1 (yourself)`

That's tree recursion.

---

## Pattern Recognition

### What clues should make you think of Tree algorithms?

✅ **Positive indicators for DFS (recursion):**
- Need to explore **every path** from root to leaves
- Computing something about a subtree that depends on what's below
- Problems with "height," "depth," "diameter," "max path sum"
- Need to detect a property that requires seeing a full subtree

✅ **Positive indicators for BFS (level-order):**
- Problems mentioning **"level"** or **"level-by-level"**
- Finding **shortest path** from root to some node
- **Connecting nodes at the same depth**
- "Minimum depth" (BFS finds it faster than DFS in worst case)

✅ **Positive indicators for Binary Search Tree (BST):**
- The tree is described as **sorted** or you need **sorted order**
- "Validate BST," "search in BST," "kth smallest in BST"
- Inorder traversal of BST gives elements in sorted order — use this!

---

## Brute Force Thinking

### Example Problem: Maximum Depth of Binary Tree

**Naive approach:** BFS level by level, count levels.

```
queue = [root]
depth = 0
while queue:
    depth += 1
    next_level = []
    for node in queue:
        if node.left:  next_level.append(node.left)
        if node.right: next_level.append(node.right)
    queue = next_level
return depth
```

**Complexity:** O(n) time, O(n) space (level can have n/2 nodes).
**Why it's not "wrong" per se:** BFS works, but DFS is more natural for depth questions and uses O(h) space instead of O(n).

---

### Example Problem: Lowest Common Ancestor
**Naive approach:** Find the path from root to node p, find the path from root to node q. The last common node in both paths is the LCA.

```
path_to_p = find_path(root, p)
path_to_q = find_path(root, q)
Find last common node between both paths
```

**Complexity:** O(n) to find each path, O(n) to compare = O(n) overall.
**But the elegant recursive solution is more insightful and shorter.**

---

## Discovering the Optimization

### Tree Recursion: The "Trust the Recursion" Mindset

The hardest part for beginners is *trusting* the recursive call. Let's build this trust:

**For Maximum Depth:**
What's the depth of a tree rooted at node `x`?

*It's 1 (for x itself) + the deeper of its two subtrees.*

```
depth(x) = 1 + max(depth(x.left), depth(x.right))
```

Base case: `depth(None) = 0` (empty tree has depth 0).

That's the entire algorithm! Two lines. The recursion handles the rest.

**The key insight for tree problems:**
> *Ask: "What would I return from THIS node, assuming the recursive calls on left and right return the correct answer for their subtrees?"*

You never need to think about how the recursion works internally — just define what each call returns and trust the process.

---

### BFS Level Order: The "Snapshot" Technique

For level-by-level processing, the key is processing exactly one level at a time.

**The insight:** At the start of each "level," take a snapshot of the queue size. Process exactly that many nodes. All nodes you add during this level belong to the NEXT level.

```
while queue:
    level_size = len(queue)   # Snapshot! How many nodes in current level?
    for _ in range(level_size):
        node = queue.popleft()
        # add children to queue for NEXT level
```

---

## Visual Walkthrough

### Maximum Depth (DFS)

```
        3
       / \
      9  20
        /  \
       15   7

max_depth(3):
  left = max_depth(9)
    left = max_depth(None) = 0
    right = max_depth(None) = 0
    return 1 + max(0, 0) = 1
  right = max_depth(20)
    left = max_depth(15)
      return 1 + max(0,0) = 1
    right = max_depth(7)
      return 1 + max(0,0) = 1
    return 1 + max(1, 1) = 2
  return 1 + max(1, 2) = 3

Answer: 3 ✓
```

---

### Level Order Traversal

```
    3
   / \
  9  20
    /  \
   15   7

Queue: [3], result = []

Level 1: size=1
  Process 3: add 9, 20
  Level result: [3]
Queue: [9, 20]

Level 2: size=2
  Process 9: no children
  Process 20: add 15, 7
  Level result: [9, 20]
Queue: [15, 7]

Level 3: size=2
  Process 15: no children
  Process 7: no children
  Level result: [15, 7]
Queue: []

Final result: [[3], [9, 20], [15, 7]] ✓
```

---

## Final Optimal Solution

### Maximum Depth (DFS Recursive)
```python
def max_depth(root: TreeNode) -> int:
    if not root:          # Base case: empty tree has depth 0
        return 0

    left_depth = max_depth(root.left)    # Depth of left subtree
    right_depth = max_depth(root.right)  # Depth of right subtree

    return 1 + max(left_depth, right_depth)   # Root + deeper subtree
```

### Level Order Traversal (BFS)
```python
from collections import deque

def level_order(root: TreeNode) -> list[list[int]]:
    if not root:
        return []

    result = []
    queue = deque([root])

    while queue:
        level_size = len(queue)   # Critical: snapshot current level size
        current_level = []

        for _ in range(level_size):
            node = queue.popleft()
            current_level.append(node.val)

            if node.left:  queue.append(node.left)
            if node.right: queue.append(node.right)

        result.append(current_level)

    return result
```

### Validate Binary Search Tree
```python
def is_valid_bst(root: TreeNode) -> bool:
    def validate(node, min_val, max_val):
        if not node:
            return True   # Empty tree is valid

        # Every node must be strictly within (min_val, max_val)
        if node.val <= min_val or node.val >= max_val:
            return False

        # Left subtree: all values must be less than current node
        # Right subtree: all values must be greater than current node
        return (validate(node.left, min_val, node.val) and
                validate(node.right, node.val, max_val))

    return validate(root, float('-inf'), float('inf'))
```

### Lowest Common Ancestor
```python
def lowest_common_ancestor(root: TreeNode, p: TreeNode, q: TreeNode) -> TreeNode:
    if not root:
        return None

    # If root is p or q, then root is the LCA
    # (the other node must be in its subtree)
    if root == p or root == q:
        return root

    left = lowest_common_ancestor(root.left, p, q)   # Search left
    right = lowest_common_ancestor(root.right, p, q)  # Search right

    if left and right:
        return root   # p found on one side, q on the other → root is LCA

    return left if left else right   # Return whichever side found something
```

### Invert Binary Tree
```python
def invert_tree(root: TreeNode) -> TreeNode:
    if not root:
        return None

    # Swap left and right children
    root.left, root.right = root.right, root.left

    # Recursively invert both subtrees
    invert_tree(root.left)
    invert_tree(root.right)

    return root
```

---

## Complexity Breakdown

### DFS Problems (Max Depth, Invert, LCA, Validate)

| Operation | Time | Space | Why |
|-----------|------|-------|-----|
| Visit every node | O(n) | — | Each node processed exactly once |
| Call stack depth | — | O(h) | h = tree height |
| Balanced tree: h = log n | O(n) | O(log n) | |
| Skewed tree: h = n | O(n) | O(n) | Worst case: like a linked list |
| **Overall** | **O(n)** | **O(h)** | |

### BFS Level Order

| Operation | Time | Space | Why |
|-----------|------|-------|-----|
| Visit every node | O(n) | — | Each node processed once |
| Queue storage | — | O(n) | Widest level can have n/2 nodes |
| **Overall** | **O(n)** | **O(n)** | |

---

## Common Mistakes

### 1. Forgetting the base case
```python
# WRONG: crashes on None input
def max_depth(root):
    return 1 + max(max_depth(root.left), max_depth(root.right))
    # Crashes when root is None!

# CORRECT: always handle None first
def max_depth(root):
    if not root:
        return 0
    return 1 + max(max_depth(root.left), max_depth(root.right))
```

### 2. Validating BST with just local comparison
```python
# WRONG: only checks parent-child, misses global BST property
def is_valid_bst(root):
    if not root: return True
    if root.left and root.left.val >= root.val: return False
    if root.right and root.right.val <= root.val: return False
    return is_valid_bst(root.left) and is_valid_bst(root.right)
# Fails for: [5, 4, 6, null, null, 3, 7]
# Node 3 is in right subtree of 5 but < 5 — should be invalid!

# CORRECT: pass min/max bounds down
def validate(node, min_val, max_val): ...
```

### 3. Not taking snapshot of queue size in BFS
```python
# WRONG: processes future levels' children in same iteration
while queue:
    node = queue.popleft()
    level.append(node.val)
    # Children added here will be processed in this same "level"!

# CORRECT: snapshot the level size first
level_size = len(queue)
for _ in range(level_size):
    node = queue.popleft()
    ...
```

### 4. Modifying tree while traversing
```python
# If you swap children and then recurse, make sure you recurse
# on the ALREADY-SWAPPED children. In invert_tree, after swap:
# root.left is the original right, root.right is the original left
# Recursing on them inverts both sides correctly
```

---

## Pattern Template

```python
# TEMPLATE 1: DFS — compute something about subtrees
def dfs_compute(root):
    if not root:
        return base_value          # e.g., 0, True, None

    left_result = dfs_compute(root.left)
    right_result = dfs_compute(root.right)

    return combine(left_result, right_result, root.val)

# TEMPLATE 2: BFS — level-by-level processing
def bfs_levels(root):
    if not root: return []
    result = []
    queue = deque([root])
    while queue:
        level_size = len(queue)
        level = []
        for _ in range(level_size):
            node = queue.popleft()
            level.append(node.val)
            if node.left:  queue.append(node.left)
            if node.right: queue.append(node.right)
        result.append(level)
    return result

# TEMPLATE 3: DFS with bounds (BST validation)
def dfs_with_bounds(root, lo=float('-inf'), hi=float('inf')):
    if not root: return True
    if not (lo < root.val < hi): return False
    return (dfs_with_bounds(root.left, lo, root.val) and
            dfs_with_bounds(root.right, root.val, hi))

# TEMPLATE 4: DFS path problems (root-to-leaf)
def path_sum(root, target):
    if not root: return False
    target -= root.val
    if not root.left and not root.right:   # Leaf check
        return target == 0
    return path_sum(root.left, target) or path_sum(root.right, target)
```

---


## Practice Problems

### 🟢 Easy: Invert Binary Tree (LeetCode #226)
**Why this problem:** Perfect first recursive tree problem. The solution is 4 lines and purely demonstrates the "swap at root, recurse on children" pattern. Forces students to trust recursion.

**Concept reinforced:** Tree recursion; in-place modification; base case.

---

### 🟡 Medium: Binary Tree Level Order Traversal (LeetCode #102)
**Why this problem:** The canonical BFS tree problem. Teaches the "snapshot the level size" technique that appears in 10+ level-related problems. Once mastered, dozens of variants become trivial.

**Concept reinforced:** BFS; level-size snapshot; deque usage.

---

### 🟠 Medium/Hard: Binary Tree Maximum Path Sum (LeetCode #124)
**Why this problem:** Requires a subtle twist — the maximum path can go through any node, not just root-to-leaf. Requires tracking two values per recursive call: the gain we can contribute to our parent, and the best path we've seen globally. Forces deep understanding of recursive return values.

**Concept reinforced:** DFS with global maximum; path through non-root; choosing what to return vs. what to track.

---
