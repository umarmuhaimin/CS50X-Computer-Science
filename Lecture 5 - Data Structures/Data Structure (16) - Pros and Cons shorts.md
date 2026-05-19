⚖️ Data Structures — Pros and Cons Summary (Lecture 5 @ CS50 | Doug Lloyd)

This is the final summary short of Lecture 5, where Doug Lloyd consolidates all four major data structures — arrays, linked lists, hash tables, and tries — and compares them across the metrics that matter: insertion, deletion, lookup, sorting, and memory. This is the note to open when you're deciding which data structure to use for a real problem.

---

## 1. 🧠 The Big Four — Overview

Everything in CS50's treatment of data structures boils down to four core ideas:

```
Arrays  →  Linked Lists  →  Hash Tables  →  Tries
```

Everything else (trees, heaps, stacks, queues) is ultimately a **variation on one of these four**:

| Variant | Based on |
|---------|---------|
| **Stack** | Array or linked list |
| **Queue** | Array or linked list |
| **Binary Search Tree** | Like a trie with 2 children per node |
| **Heap** | Tree-based, similar to tries |
| **Hash table with chaining** | Array + linked lists combined |

Understanding the four fundamentals gives you the mental model for everything else.

---

## 2. 📊 How to Choose — The Framework

Choosing a data structure is always a **trade-off**. There is no universally "best" data structure — each excels in certain situations and struggles in others.

The five metrics to evaluate for any data structure:

| Metric | Question to ask |
|--------|----------------|
| **Insertion** | How hard is it to add new data? |
| **Deletion** | How hard is it to remove data? |
| **Lookup** | How fast can you find data? |
| **Sorting** | Can/does it keep data in order? |
| **Size** | How much memory does it consume? |

For each metric, ask: *what does my program actually need to do most?* The data structure that excels at your primary operation is usually the right choice.

---

## 3. 📐 Arrays — Detailed Analysis

```c
int arr[10];     // fixed-size, contiguous memory
arr[5] = 42;     // instant access to any element
```

### Memory layout

```
Index:   0    1    2    3    4    5    6    7    8    9
       ┌────┬────┬────┬────┬────┬────┬────┬────┬────┬────┐
arr:   │ 12 │ 7  │ 3  │ 45 │ 18 │ 42 │ 9  │ 27 │ 61 │ 5  │
       └────┴────┴────┴────┴────┴────┴────┴────┴────┴────┘
         ↑                              ↑
   arr[0] O(1)                    arr[5] = 42, also O(1)
```

### Insertion — BAD

Inserting at the **end** is fine — just put it there. But inserting **anywhere else** requires shifting everything after the insertion point:

```
Insert 99 at index 2:
Before: [ 12 | 7  | 3  | 45 | 18 | 42 ]
                ↑ insert here

Must shift 3, 45, 18, 42 all one step right:
Step 1: [ 12 | 7  | 3  | 45 | 18 | 42 | ? ]
Step 2: [ 12 | 7  | 3  | 45 | 18 | ?  | 42 ]
Step 3: [ 12 | 7  | 3  | 45 | ?  | 18 | 42 ]
Step 4: [ 12 | 7  | 3  | ?  | 45 | 18 | 42 ]
Step 5: [ 12 | 7  | 99 | 3  | 45 | 18 | 42 ]

Cost: O(n) — proportional to elements shifted
```

This is exactly what insertion sort does — and you already know it's expensive.

### Deletion — BAD

Removing from the end is fine. Removing from the middle leaves a gap, and closing that gap requires shifting:

```
Delete element at index 3 (value 45):
Before: [ 12 | 7  | 3  | 45 | 18 | 42 ]
                          ↑ delete

After:  [ 12 | 7  | 3  |    | 18 | 42 ]   ← gap! unusable hole
                          ↑

To close:  [ 12 | 7  | 3  | 18 | 42 |    ]
Cost: O(n) — must shift remaining elements
```

### Lookup — GREAT ✅

Random access in O(1). The formula `base_address + (index × element_size)` computes the exact memory address instantly — no traversal needed.

```
arr[7]  →  jump directly to that address  →  O(1)
arr[0]  →  jump directly to that address  →  O(1)
arr[99] →  jump directly to that address  →  O(1)
```

Every lookup takes exactly the same amount of time regardless of array size.

### Sorting — EASY ✅

Every classic sorting algorithm (selection sort, insertion sort, bubble sort, merge sort) operates naturally on arrays because random access makes comparisons and swaps efficient. Arrays are the go-to structure for sorting.

### Size — SMALL ✅

No overhead. Each element takes exactly its own memory. No pointers, no extra bookkeeping.

```
int arr[10] = exactly 10 × 4 bytes = 40 bytes
```

### Flexibility — BAD ❌

Fixed at declaration. If you need more space, you must:
1. Declare a new, larger array
2. Copy all elements
3. Free the old array
4. Hope your new size estimate is right

If you miscalculate again, repeat the whole process.

### Arrays at a glance

| | Rating | Why |
|--|--------|-----|
| **Insertion** | ❌ Bad | Shifting required for middle/front |
| **Deletion** | ❌ Bad | Shifting required to close gaps |
| **Lookup** | ✅ Great | Random access O(1) |
| **Sorting** | ✅ Easy | All sorting algorithms use arrays |
| **Size** | ✅ Small | No overhead, just the data |
| **Flexibility** | ❌ None | Fixed size, no grow/shrink |

---

## 4. 🔗 Linked Lists — Detailed Analysis

```c
typedef struct node { int val; struct node *next; } node;
node *list = NULL;
```

### Memory layout

```
list
  │
  ▼
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│ val: 12     │     │ val: 7      │     │ val: 3      │
│ next: ──────┼────►│ next: ──────┼────►│ next: NULL  │
└─────────────┘     └─────────────┘     └─────────────┘
  0x100               0x500               0x2A0
```

Nodes can be anywhere in memory — connected only by pointers.

### Insertion — EASY ✅

Prepending to the front:

```
new->next = list;   // new node points to old head
list = new;         // new node becomes new head

Always O(1) — constant number of pointer updates, regardless of list length.
```

### Deletion — EASY (once found) ✅

Finding the element requires traversal — O(n). But the actual deletion is just pointer updates:

```
// Delete node in middle:
prev->next = target->next;   // skip over target
free(target);                 // release memory

Only 2 operations. No shifting. O(1) for the deletion itself.
```

For doubly-linked lists: also update `target->next->prev`. Still O(1).

### Lookup — BAD ❌

No random access. Must start at the head and walk forward:

```
Find value 42:
list → [12] → [7] → [3] → [45] → [42] ← found, 5 steps
         ↑     ↑     ↑     ↑     ↑
       check check check check check

O(n) — in the worst case, check every element
```

If you do a lot of searching, linked lists are poor performers.

### Sorting — DIFFICULT ❌

The only practical way to keep a linked list sorted is to sort-as-you-insert: find the right position during insertion. But then:

```
Sorted insertion: O(n) per insertion (must find the right spot)
vs.
Prepend insertion: O(1) per insertion (but unsorted)
```

You lose the main advantage of linked lists (fast insertion) the moment you try to sort them. For pre-sorted data, just use an array.

### Size — SMALL (slightly larger than arrays) 🔶

```
Singly-linked node: 4 bytes (int) + 8 bytes (pointer) = 12 bytes per element
Array:              4 bytes (int) per element

Overhead: 8 extra bytes per element (the next pointer)
For 1,000 elements: 8 KB extra — acceptable in most situations
```

### Flexibility — GREAT ✅

Grows and shrinks freely at runtime. No copying needed.

### Linked Lists at a glance

| | Rating | Why |
|--|--------|-----|
| **Insertion** | ✅ Easy | Prepend in O(1) |
| **Deletion** | ✅ Easy | Once found: just update pointers |
| **Lookup** | ❌ Bad | Linear search only, O(n) |
| **Sorting** | ❌ Difficult | Sort-as-you-insert kills insertion speed |
| **Size** | 🔶 Small | Slightly larger than arrays (pointer overhead) |
| **Flexibility** | ✅ Great | Grows/shrinks dynamically |

---

## 5. #️⃣ Hash Tables — Detailed Analysis

```c
node *table[1000];    // array of 1000 linked list heads
```

### Memory layout

```
table[0]  ──► NULL
table[1]  ──► NULL
table[2]  ──► ["Ross" | ──►] ──► NULL
table[3]  ──► NULL
...
table[6]  ──► ["Phoebe" | ──►] ──► ["Joey" | NULL]
...
table[9]  ──► NULL
```

A hash table is an array where each slot holds the head of a linked list.

### Insertion — STRAIGHTFORWARD 🔶

Two steps:
1. Run data through hash function → get hash code (fast)
2. Insert at `table[hash_code]` — prepend to linked list (O(1))

```
hash("Joey") = 6
Prepend "Joey" to the chain at table[6]   ← O(1)
Total: O(1) for the insert itself
```

The hash function call is O(k) where k = length of data, but k is typically a small constant.

### Deletion — EASY (once found) ✅

```
// With chaining:
1. hash the data to find the right bucket → O(k)
2. linear search the chain at that bucket → O(n/k)
3. update pointers, free node → O(1)
```

Without collisions: just hash, go to that slot, delete directly — very fast.

### Lookup — BETTER THAN LINKED LIST 🔶

Still technically O(n) in the worst case (all elements hash to same bucket). But in practice, with a good hash function and many buckets:

```
One big linked list of 1000 elements: O(1000) to search
Hash table with 100 buckets:          O(10) to search (10 per bucket)
Hash table with 1000 buckets:         O(1) to search in practice

The real-world constant factor makes hash tables dramatically faster.
```

This is why hash tables are so widely used — they don't achieve true O(1) theoretically (with chaining), but practically they come extremely close.

### Sorting — BAD ❌

Hash tables deliberately scatter data across buckets to minimise collisions. This destroys any natural ordering. If you need sorted data, use an array and sort it — don't fight the hash table.

### Size — VARIABLE 🔶

```
Small hash table + many elements = long chains = slow lookup
Large hash table + few elements  = lots of wasted empty buckets

Generally: hash table > linked list > array for same data
But exactly how much bigger depends entirely on bucket count.
```

There's no single answer — it's a tuning decision based on your data.

### Hash Tables at a glance

| | Rating | Why |
|--|--------|-----|
| **Insertion** | 🔶 Two-step | Hash, then add — generally fast |
| **Deletion** | ✅ Easy | Once found: update pointers |
| **Lookup** | 🔶 Better than list | Real-world constant factor helps enormously |
| **Sorting** | ❌ Bad | Data scattered by hash — use arrays instead |
| **Size** | 🔶 Variable | Bigger than linked list, smaller than trie |

---

## 6. 🌳 Tries — Detailed Analysis

```c
typedef struct _trie { char val[20]; struct _trie* paths[10]; } trie;
```

### Memory layout

```
ROOT (node with 10 pointers)
  │
  └──[1]──► NODE
               ├──[6]──► NODE
               │            └──[3]──► NODE
               │                        └──[6]──► "Harvard"
               └──[7]──► NODE
                            └──[0]──► NODE
                                         └──[1]──► "Yale"
```

Each node contains the data AND an array of 10 (or 26) pointers to child nodes.

### Insertion — COMPLEX but O(1) 🔶

**Complex** because of the pointer navigation and repeated malloc calls. For each digit of the key, you must:
1. Check if `paths[digit]` is NULL
2. If NULL: `malloc` a new node
3. Follow the pointer to the next level

```
Insert "Harvard" (key 1636): 4 digits = up to 4 malloc calls
Insert "Yale" (key 1701):    some nodes already exist from Harvard!
Insert "Dartmouth" (key 1769): shares more existing nodes
```

**But it's O(1)**: you always make exactly k operations where k = key length. k is a constant. The complexity comes from the implementation, not the algorithm.

> Doug Lloyd: *"It's only the human element that makes it tricky — having to encounter null pointers, malloc space, go there, possibly malloc from there again."*

### Deletion — EASY ✅

Navigate the path using the key (always k steps), then free the node. No shifting, no pointer chain adjustments.

```
Find "Harvard" at path 1→6→3→6: 4 pointer follows
Free that leaf node
Done. O(1)
```

### Lookup — FAST (effectively O(1)) ✅

```
Find "Harvard" (key 1636):
  Step 1: follow paths[1]
  Step 2: follow paths[6]
  Step 3: follow paths[3]
  Step 4: follow paths[6]
  Step 5: check value → "Harvard" ✅

Always 4 steps for a 4-digit key. Always k steps for a k-length key.
k is constant. Therefore O(1).
```

This is the "holy grail" — truly constant-time lookup regardless of how many elements are in the trie.

### Sorting — ALREADY SORTED ✅

By construction, a trie naturally produces alphabetical/numerical order. When you traverse a trie from left to right (smallest digit/character to largest), you get all entries in sorted order — for free.

```
Traversal order of the university trie:
1636 → Harvard   (comes first)
1701 → Yale      (comes second)
1769 → Dartmouth (comes third)

This is already sorted by founding year!
```

### Size — HUGE ❌

This is the critical trade-off:

```
One trie node (digit keys, paths[10]):
  data field:      variable
  10 pointers:     10 × 8 bytes = 80 bytes
  Minimum per node: 80 bytes (just the pointers!)

One trie node (letter keys, paths[26]):
  26 pointers:     26 × 8 bytes = 208 bytes
  Plus uppercase:  26 more → 52 pointers = 416 bytes per node

If you have capital + lowercase + digits:
  62 pointers × 8 bytes = 496 bytes per node
```

Even with just a few elements, the trie allocates enormous amounts of memory because most pointer slots will be NULL — wasted space.

### Tries at a glance

| | Rating | Why |
|--|--------|-----|
| **Insertion** | 🔶 Complex but O(1) | Many malloc calls, but always k steps |
| **Deletion** | ✅ Easy | Navigate, free, done |
| **Lookup** | ✅ Fast (O(1)) | Always exactly k steps |
| **Sorting** | ✅ Already sorted | Alphabetical order by construction |
| **Size** | ❌ Huge | Each node has 10–26+ pointers ≥ 80–200 bytes |

---

## 7. ⚖️ The Complete Comparison Table

| Metric | Array | Linked List | Hash Table | Trie |
|--------|-------|------------|-----------|------|
| **Insertion** | ❌ Bad (shifting) | ✅ Easy (prepend O(1)) | 🔶 Two-step (hash+add) | 🔶 Complex but O(1) |
| **Deletion** | ❌ Bad (shifting) | ✅ Easy (pointers) | ✅ Easy once found | ✅ Easy (navigate+free) |
| **Lookup** | ✅ Great (O(1) random) | ❌ Bad (O(n) linear) | 🔶 Better than list | ✅ Fast (O(k) = O(1)) |
| **Sorting** | ✅ Easy | ❌ Difficult | ❌ Not suitable | ✅ Already sorted |
| **Memory** | ✅ Smallest | 🔶 Small | 🔶 Variable | ❌ Huge |
| **Flexibility** | ❌ Fixed size | ✅ Dynamic | ✅ Dynamic | ✅ Dynamic |
| **Collisions** | N/A | N/A | ❌ Possible | ✅ Never |

### Complexity summary

| Operation | Array | Linked List | Hash Table | Trie |
|-----------|-------|------------|-----------|------|
| **Insert** | O(n) mid / O(1) end | O(1) | O(1) avg | O(k) |
| **Delete** | O(n) mid / O(1) end | O(n) find + O(1) delete | O(n/k) find + O(1) delete | O(k) |
| **Lookup** | O(1) | O(n) | O(n/k) ≈ O(1) | O(k) |
| **Sort** | O(n log n) | O(n²) naive | Not applicable | O(1) (already done) |

Where k = key length (constant in practice).

---

## 8. 🎯 The Decision Guide — When to Use Which

### Choose an **Array** when:

- You need **fast random access** (looking up elements by index)
- You will be **sorting** the data frequently
- The **number of elements is fixed** or known in advance
- **Memory efficiency** is important
- You need to use binary search

```
Best for: databases, sorting algorithms, fixed-size buffers, lookup tables
```

---

### Choose a **Linked List** when:

- You **frequently insert/delete** at the front or middle
- The **number of elements varies** greatly at runtime
- You don't need to search often
- You're implementing a **stack or queue**

```
Best for: stacks, queues, undo history, dynamic collections with frequent modification
```

---

### Choose a **Hash Table** when:

- You need **fast lookup** without caring about order
- You're building a **dictionary**, map, or set
- You want near-constant-time operations in practice
- You can afford moderate memory usage
- You're implementing a **cache or symbol table**

```
Best for: dictionaries, caches, phone books, spell-checkers (when memory matters)
```

---

### Choose a **Trie** when:

- You need **guaranteed constant-time** lookup, insert, and delete
- Your keys are **strings or fixed-length sequences**
- You need **prefix-based searching** (autocomplete)
- Data naturally needs to be **sorted as you insert**
- Memory is **not** a primary constraint

```
Best for: autocomplete systems, routing tables, spell-checkers (when speed matters most), DNS lookup
```

---

### Decision flowchart

```
Start: What operation do you do most?
         │
         ├──► Lookup by index / random access?
         │         └──► ARRAY
         │
         ├──► Frequent insert/delete, order doesn't matter?
         │         └──► LINKED LIST
         │
         ├──► Fast lookup by key, some memory available?
         │         └──► HASH TABLE
         │
         └──► Fastest possible lookup, prefix search, memory available?
                   └──► TRIE
```

---

## 9. 📌 Summary

| Data Structure | Best strength | Worst weakness |
|----------------|--------------|----------------|
| **Array** | O(1) random access, easy sorting | Fixed size, slow insert/delete |
| **Linked List** | O(1) insert/delete at front, flexible size | O(n) lookup, hard to sort |
| **Hash Table** | Practical O(1) everything, no order needed | Uses more memory, bad for sorting |
| **Trie** | True O(1) lookup, self-sorting | Enormous memory consumption |

### The fundamental trade-off

```
Speed ←────────────────────────────── Memory ──────────────────────────────────►

Array          Linked List          Hash Table                    Trie
(O(1) lookup   (O(1) insert         (O(1) practical              (O(1) guaranteed
 tiny memory)   small memory)        medium memory)               massive memory)
```

### Doug Lloyd's final take

> *"You don't necessarily always need to use the super fast insertion, deletion, and lookup of a trie if you really don't care about inserting and deleting too much. If you need just quickly random access, maybe an array is better."*

The right data structure is the one that best fits your **actual use case** — not the one with the most impressive theoretical complexity. Understanding each structure's strengths and weaknesses is what separates good programmers from great ones.
