🌳 Trees — Binary Search Trees (Lecture 5 — Data Structures @ CS50)

We've hit a wall. Arrays give us O(log n) binary search but can't grow easily. Linked lists grow easily but lose binary search entirely. The question is: **can we have both?** The answer is yes — using a structure called a **binary search tree**.

---

## 1. 🧠 The Problem to Solve

Here's where we stand after studying arrays and linked lists:

| Data Structure | Growth | Search |
|----------------|--------|--------|
| Array | ❌ Requires copying everything | ✅ O(log n) binary search |
| Linked List | ✅ Just add a node | ❌ O(n) — binary search impossible |

The root cause of linked lists' search problem: you can't jump to the middle. To find the midpoint, you have to traverse the whole list first. The pointer-chain structure only goes in one direction.

What if nodes could have **two pointers** — one pointing left, one pointing right — giving us a two-dimensional structure instead of a one-dimensional chain?

---

## 2. 📐 From Sorted Array to Binary Search Tree

Start with a sorted array of 7 numbers:

```
Index:   0    1    2    3    4    5    6
Value:  [1]  [2]  [3]  [4]  [5]  [6]  [7]
```

When you run binary search, you always jump to the **middle** first (index 3 → value 4), then to the middle of each remaining half. Now arrange these values **vertically**: the middle sits at the top, the sub-middles hang below it, and so on:

```
Binary Search Tree:
            [4]          ← root (middle of the array)
           /   \
         [2]   [6]       ← middles of each half
        /   \ /   \
      [1] [3] [5] [7]    ← the leaf nodes (no children)
```

This is a **binary search tree (BST)**. Every value you'd jump to during binary search is now a node in this tree. You've taken the one-dimensional array and exploded it into two dimensions — and the pointer arrows connect everything together.

---

## 3. 🔑 The BST Property (The Key Rule)

For every node in a binary search tree:
- All values in the **left subtree** are **smaller** than the node's value
- All values in the **right subtree** are **larger** than the node's value

This property must hold **recursively** — meaning it applies at every node, not just the root.

```
            [4]
           /   \
         [2]   [6]
        /   \ /   \
      [1] [3] [5] [7]

Root (4): everything left (1,2,3) < 4 < everything right (5,6,7) ✅
Node (2): everything left (1) < 2 < everything right (3) ✅
Node (6): everything left (5) < 6 < everything right (7) ✅
```

- **Clarification — "recursive property":** The BST property isn't just that a node is bigger than its immediate left child and smaller than its immediate right child. It means the node is bigger than *everything* in its left subtree and smaller than *everything* in its right subtree. This same rule holds for every node in the tree — it's self-similar, just like binary search itself.

---

## 4. 💻 Node Structure in Code

A BST node needs three things: the data, a pointer to the left child, and a pointer to the right child.

```c
typedef struct node
{
    int number;
    struct node *left;   // pointer to left child (smaller values)
    struct node *right;  // pointer to right child (larger values)
}
node;
```

Compare to a linked list node:

```c
// Linked list node — 1 pointer (one direction only)
typedef struct node
{
    int number;
    struct node *next;
} node;

// BST node — 2 pointers (left and right branches)
typedef struct node
{
    int number;
    struct node *left;
    struct node *right;
} node;
```

The only structural difference is one extra pointer. But that single addition changes everything — now nodes can branch in two directions, forming a two-dimensional structure.

Memory cost per node:
- Plain int array: 4 bytes (just the int)
- Linked list node: 4 + 8 = 12 bytes (int + 1 pointer)
- BST node: 4 + 8 + 8 = 20 bytes (int + 2 pointers) — roughly **3x more memory** than the array

---

## 5. 🔍 Searching a Binary Search Tree — Recursive Code

Searching a BST maps elegantly to recursion: each step is the same decision (go left or go right), applied to a smaller subtree.

```c
bool search(node *tree, int number)
{
    if (tree == NULL)
    {
        return false;                       // base case: empty tree / gone past a leaf
    }
    else if (number < tree->number)
    {
        return search(tree->left, number);  // smaller → search left subtree
    }
    else if (number > tree->number)
    {
        return search(tree->right, number); // larger → search right subtree
    }
    else
    {
        return true;                        // found it!
    }
}
```

### Step-by-step example: searching for 5

```
Tree:
            [4]
           /   \
         [2]   [6]
        /   \ /   \
      [1] [3] [5] [7]

Step 1: tree = root (4). Is 5 > 4? Yes → go RIGHT to (6)
Step 2: tree = (6).     Is 5 < 6? Yes → go LEFT to (5)
Step 3: tree = (5).     Is 5 == 5? Yes → return true ✅

Total: 3 steps for 7 elements.
```

### Searching for a value that doesn't exist

```
Searching for 9:

Step 1: 9 > 4 → go right to (6)
Step 2: 9 > 6 → go right to (7)
Step 3: 9 > 7 → go right to NULL
Step 4: tree == NULL → return false ❌
```

The `if (tree == NULL) return false` is the **base case** — the stopping condition that prevents the recursion from going on forever. When you've followed a pointer and it's NULL, you've gone past a leaf — the value isn't in the tree.

---

## 6. ⏱️ Complexity of a Binary Search Tree

| Operation | Time Complexity | Reason |
|-----------|----------------|--------|
| **Search** | O(log n) | Each step halves the remaining nodes — like binary search |
| **Insert** | O(log n) | Walk down the tree to find the right position |
| **Delete** | O(log n) | Find the node, then rewire the pointers |

This is the mashup we wanted: linked-list-style growth (no copying to add a new node) combined with array-style binary search speed.

---

## 7. ⚠️ The Unbalanced Tree Problem

There's a critical catch. The O(log n) guarantee only holds when the tree is **balanced** — roughly equal numbers of nodes on the left and right sides.

What if values are inserted in sorted order (1, 2, 3, 4, 5...)?

```
Insert 1:    [1]
Insert 2:    [1]
                \
                [2]
Insert 3:    [1]
                \
                [2]
                   \
                   [3]
Insert 4, 5:  keeps extending right...
```

The tree degenerates into essentially a **linked list** going in one direction. Searching it becomes O(n) again — you're no longer halving the problem at each step.

The BST property technically isn't violated (every node is still greater than its left child), but the structure is completely unbalanced.

**Solution:** Advanced algorithms called **self-balancing trees** (like AVL trees or Red-Black trees) automatically rebalance the tree after each insertion. This is covered in more advanced CS courses — but it's worth knowing the limitation exists.

---

## 8. ⚖️ Binary Search Tree vs. Array vs. Linked List

| Aspect | Array | Linked List | BST (balanced) |
|--------|-------|-------------|----------------|
| **Memory layout** | Contiguous | Scattered | Scattered |
| **Growth** | Must copy | Add node, no copying | Add node, no copying |
| **Search** | O(log n) | O(n) | O(log n) |
| **Insert** | O(n) (shift elements) | O(1) prepend | O(log n) |
| **Binary search** | ✅ Yes | ❌ No | ✅ Yes |
| **Memory per element** | 4 bytes | 12 bytes | 20 bytes |
| **Downside** | Fixed size | No binary search | More memory; can become unbalanced |

---

## 9. 📌 Summary

| Concept | Detail |
|---------|--------|
| **Binary search tree** | Linked structure where each node has up to 2 children: left (smaller) and right (larger) |
| **BST property** | Left subtree < node < right subtree — holds recursively at every node |
| **Root** | The topmost node — entry point to the tree |
| **Leaf** | A node with no children (both left and right are NULL) |
| **Node struct** | `int number; struct node *left; struct node *right;` |
| **Search logic** | If target < node → go left; if target > node → go right; if equal → found |
| **Search complexity** | O(log n) when balanced |
| **Unbalanced worst case** | O(n) — tree becomes a chain when values inserted in sorted order |
| **Memory cost** | ~3x more than a plain int array (20 bytes vs 4 bytes per element) |
| **Key win over linked list** | Binary search restored — each step halves the problem |
| **Key win over array** | Dynamic growth — add nodes without copying existing data |
