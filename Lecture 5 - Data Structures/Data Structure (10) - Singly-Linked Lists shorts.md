🔗 Singly-Linked Lists (Lecture 5 — Data Structures @ CS50 | Doug Lloyd)

A singly-linked list is one of the most important data structures in computer science. It solves the two biggest limitations of arrays — costly insertion and fixed size — by chaining together individually allocated nodes through pointers. This short by Doug Lloyd covers everything: the structure, all five core operations, and the critical pitfalls that break the chain.

---

## 1. 🧠 Why Do We Need Linked Lists? — The Problems with Arrays

Before building a linked list, you have to understand *why* arrays aren't enough.

### Problem 1 — Insertion is expensive

Arrays require all elements to be contiguous in memory. Inserting into the middle means shifting every element after the insertion point one spot to the right — just like insertion sort does. The bigger the array, the more expensive every insertion becomes.

```
Insert 4 into position 2 of [ 1 | 2 | 5 | 7 | 9 ]:

Step 1: shift 9 right    [ 1 | 2 | 5 | 7 | _ | 9 ]
Step 2: shift 7 right    [ 1 | 2 | 5 | _ | 7 | 9 ]
Step 3: shift 5 right    [ 1 | 2 | _ | 5 | 7 | 9 ]
Step 4: insert 4         [ 1 | 2 | 4 | 5 | 7 | 9 ]
```

That's O(n) just for one insertion. The larger the array, the slower it gets.

### Problem 2 — Size is fixed at declaration

When you declare an array, you commit to a size immediately and forever:

```c
int list[100];   // you get exactly 100 ints — no more, no less
```

If you guessed wrong and need 101, you have to:
1. Declare a new, bigger array
2. Copy every element from the old to the new
3. Free the old one
4. What if you need 102 next? Do it all again.

This is not just tedious — for large datasets it becomes seriously slow, and you're always either wasting memory (allocated too much) or scrambling to resize (allocated too little).

### The solution — combine pointers + structs + malloc

By combining what you know about **pointers**, **structs**, and **dynamic memory allocation** (`malloc`), you can build a data structure that:
- Grows and shrinks on demand (no copying)
- Wastes no space (allocate only what you need, when you need it)
- Can insert at the front in O(1) — constant time, always

That data structure is a **singly-linked list**.

---

## 2. 🔗 What Is a Singly-Linked List?

A singly-linked list is a **chain of nodes** where each node contains:
1. **Data** — the actual value you care about
2. **A pointer** — the address of the *next* node in the chain

```
Singly-linked list with 3 nodes:

list (head)
  │
  ▼
┌───────┐     ┌───────┐     ┌───────┐
│  val  │     │  val  │     │  val  │
├───────┤     ├───────┤     ├───────┤
│  ──────────►│  ──────────►│  NULL │
└───────┘     └───────┘     └───────┘
  Node 1         Node 2         Node 3
```

Each node knows only one thing about the rest of the list: **where the next node is**. The last node's `next` pointer is `NULL` — that's how you know you've reached the end.

### Why is this better than an array?

Because the nodes don't need to be next to each other in memory. Node 1 might be at address `0x100`, Node 2 at `0x500`, Node 3 at `0x2A0` — completely scattered. It doesn't matter, because each node *remembers* where the next one is. You only need to follow the arrows.

```
Memory (realistic):
0x100: [ val:1 | next:0x500 ]    ← Node 1
...
0x500: [ val:2 | next:0x2A0 ]    ← Node 2
...
0x2A0: [ val:3 | next:NULL  ]    ← Node 3
```

### Singly vs. Doubly

A **singly-linked list** has arrows pointing in one direction only (forward). Each node knows the next, but not the previous.

A **doubly-linked list** has arrows in both directions — each node knows both the next *and* the previous. Doug Lloyd covers that in a separate short.

---

## 3. 🏗️ The Node Struct — Defining the Building Block

In C, a linked list node is a struct with two fields:

```c
typedef struct sllist
{
    VALUE val;            // the data — any data type (int, char, float...)
    struct sllist* next;  // pointer to the next node
}
sllnode;
```

### Why `VALUE` and not `int`?

`VALUE` is a placeholder — Doug Lloyd uses it to show this works with *any* data type. In practice you'd replace `VALUE` with `int`, `char *`, `float`, or whatever your program needs. It's not restricted to integers.

### Why does the struct need TWO names? ⚠️

Look closely: the struct is called `struct sllist` at the top, and `sllnode` at the bottom.

```c
typedef struct sllist    ← temporary name (used INSIDE the struct)
{
    VALUE val;
    struct sllist* next; ← uses the temporary name — must already exist!
}
sllnode;                 ← permanent name (only exists AFTER the closing brace)
```

The C compiler reads top-to-bottom. When it reaches `struct sllist* next`, the name `sllnode` doesn't exist yet — the definition isn't finished. So you can't write `sllnode* next`.

But `struct sllist` *already exists* at that point (you named it on the first line), so you can use that as the type for the `next` field.

- **Clarification — self-referential struct:** A struct that contains a pointer to another struct of the same type is called a **self-referential** struct. This is exactly what a node is — each node contains a pointer to another node. All self-referential structs in C need this two-name pattern.

### What does a node look like in memory?

```
One sllnode:
┌────────────────┐
│   val          │  ← 4 bytes (if int)
├────────────────┤
│   next  ──────────► (address of next node, or NULL)
└────────────────┘    ← 8 bytes (pointer on 64-bit)
Total: ~12 bytes per node
```

The top box holds the data. The bottom box holds the pointer. When the pointer is NULL (drawn as a filled/red box in Doug's slides), there is no next node — it's the end of the list.

---

## 4. 📋 The 5 Core Operations

To work with a singly-linked list effectively, you need five operations:

| # | Operation | Purpose | Function prototype |
|---|-----------|---------|-------------------|
| 1 | **Create** | Start a new list from nothing | `sllnode* create(VALUE val);` |
| 2 | **Search (Find)** | Check if a value exists in the list | `bool find(sllnode* head, VALUE val);` |
| 3 | **Insert** | Add a new node to an existing list | `sllnode* insert(sllnode* head, VALUE val);` |
| 4 | **Delete (single)** | Remove one specific node | *(tricky — see Section 8)* |
| 5 | **Destroy** | Free every node — delete entire list | `void destroy(sllnode* head);` |

---

## 5. ➕ Operation 1: Create — Building a Node From Nothing

```c
sllnode* create(VALUE val);
```

This function creates a single new node from scratch and returns a pointer to it.

### Steps

```
a. Dynamically allocate space for a new sllnode
b. Check to make sure we didn't run out of memory (NULL check)
c. Initialize the node's val field
d. Initialize the node's next field to NULL
e. Return a pointer to the newly created sllnode
```

### Visual walkthrough — `sllnode* new = create(6);`

**Step a — malloc a new node (memory allocated, garbage values inside):**
```
        ┌─────────┐
        │    ?    │  ← garbage value
        ├─────────┤
        │    ?    │  ← garbage value
        └─────────┘
            ↑
           new
```

**Step b — check for NULL:** If `malloc` returned NULL, something went wrong (out of memory). Stop the program. Otherwise, continue.

**Step c — set val to 6:**
```
        ┌─────────┐
        │    6    │  ← val is now 6
        ├─────────┤
        │    ?    │  ← next still garbage
        └─────────┘
            ↑
           new
```

**Step d — set next to NULL (this is the only node, nothing comes after it):**
```
        ┌─────────┐
        │    6    │
        ├─────────┤
        │  [NULL] │  ← end of list
        └─────────┘
            ↑
           new
```

**Step e — return the pointer `new`:**

The caller receives a pointer to this freshly created node. You now have a linked list with one element.

```
new ──► [ 6 | NULL ]
```

---

## 6. 🔍 Operation 2: Find — Searching the List

```c
bool find(sllnode* head, VALUE val);
```

This function traverses the list from beginning to end, looking for a value. Returns `true` if found, `false` if not.

### Steps

```
a. Create a traversal pointer (trav) pointing to the list's head
b. If the current node's val is what we're looking for → return true (success)
c. If not → advance trav to the next node, go back to step b
d. If trav reaches NULL → return false (end of list, not found)
```

### Why not just move `head` directly?

> **Critical rule:** Always keep a pointer to the first node of the list. Never move it.

If you moved `head` around to traverse the list, you'd lose your reference to the beginning — the entire list before your current position would be gone. That's a memory leak.

The solution: create a **second pointer** called `trav` (short for traversal) that starts at the same place as `head`, but is used for moving. `head` never moves.

```c
sllnode *trav = head;   // trav starts at head
                        // head stays exactly where it is, forever
```

- **Clarification — no malloc for trav:** You do NOT call `malloc` for `trav`. The node it points to already exists in memory. You're just creating another pointer variable (on the stack) that holds the same address. No new memory is allocated.

### Visual walkthrough — `find(list, 6)` on list `[2 → 3 → 5 → 6 → 8]`

```
list ──► [2|→] ──► [3|→] ──► [5|→] ──► [6|→] ──► [8|NULL]
          ↑
         trav     Step 1: Is 2 == 6? No → advance trav

list ──► [2|→] ──► [3|→] ──► [5|→] ──► [6|→] ──► [8|NULL]
                    ↑
                   trav     Step 2: Is 3 == 6? No → advance trav

list ──► [2|→] ──► [3|→] ──► [5|→] ──► [6|→] ──► [8|NULL]
                               ↑
                              trav     Step 3: Is 5 == 6? No → advance trav

list ──► [2|→] ──► [3|→] ──► [5|→] ──► [6|→] ──► [8|NULL]
                                          ↑
                                         trav     Step 4: Is 6 == 6? YES ✅
                                                  → return true
```

### Visual walkthrough — `find(list, 6)` on list `[2 → 7 → 5 → 3 → 8]` (not found)

Notice: linked lists don't have to be sorted. There's no index — elements can be in any order.

```
list ──► [2|→] ──► [7|→] ──► [5|→] ──► [3|→] ──► [8|NULL]
          ↑
         trav    Is 2 == 6? No

          ... (advance through 7, 5, 3 — none match) ...

list ──► [2|→] ──► [7|→] ──► [5|→] ──► [3|→] ──► [8|NULL]
                                                     ↑
                                                    trav    Is 8 == 6? No
                                                            trav = trav->next = NULL

trav is now NULL → reached the end of the list
→ return false ❌
```

### No random access ⚠️

Unlike arrays, you cannot jump directly to element 5 or element 12. There's no index. You must always start at the head and follow the arrows. This means search is always **O(n)** — linear time.

---

## 7. 📥 Operation 3: Insert — Adding a Node at the Front

```c
sllnode* insert(sllnode* head, VALUE val);
```

This function creates a new node and inserts it at the **beginning** of an existing list. It returns a pointer to the new head.

### Steps

```
a. Dynamically allocate space for a new sllnode
b. Check to make sure we didn't run out of memory
c. Populate and insert the node AT THE BEGINNING of the linked list
d. Return a pointer to the new head of the linked list
```

### Why always insert at the beginning?

You have a pointer to the beginning of the list (the head). You do **not** have a pointer to the end. So to insert at the end, you'd have to traverse the entire list first — that's O(n). But inserting at the front is always O(1): you're already at the front, it takes the same number of steps no matter how long the list is.

### Visual walkthrough — `list = insert(list, 12)` on `[15 → 9 → 13 → 10]`

**Starting state:**
```
list ──► [15|→] ──► [9|→] ──► [13|→] ──► [10|NULL]
```

**Step a — malloc a new node, put 12 inside:**
```
new ──► [12|?]      list ──► [15|→] ──► [9|→] ──► [13|→] ──► [10|NULL]
```

Now a decision: which pointer do we move first?

---

## 8. ⚠️ The Critical Pointer Order Rule — Order Matters!

This is **one of the trickiest things about linked lists**. Moving pointers in the wrong order permanently destroys part of your list.

### The WRONG way — update `list` first ❌

```
Step 1 (wrong): Move list to point to new node 12

list ──► [12|?]      [15|→] ──► [9|→] ──► [13|→] ──► [10|NULL]
```

Now `list` points to node 12. But node 12's `next` is garbage — it doesn't point to 15. And we have NO pointer to 15 anymore. The old chain is **orphaned**. There is no way to reconnect them.

```
[15] → [9] → [13] → [10]   ← permanently lost, leaked forever ❌
```

### The CORRECT way — connect new node FIRST, then update head ✅

```
Step 1 (correct): Set new node's next to point to the old head (15)

new ──► [12|→] ──────────────► [15|→] ──► [9|→] ──► [13|→] ──► [10|NULL]
                                 ↑
                               list still points here (unchanged)
```

Now the new node is connected to the existing chain. Nothing is orphaned.

```
Step 2: Update list to point to new node (new head is now 12)

list ──► [12|→] ──► [15|→] ──► [9|→] ──► [13|→] ──► [10|NULL]
```

The list is now `[12 → 15 → 9 → 13 → 10]`. ✅

### The rule in two lines

```
1. new->next = list;   // connect new node to old list FIRST
2. list = new;         // THEN update head to the new node
```

> **Never reverse this order.** Step 1 must always come before Step 2. If you move `list` first, you lose the old chain permanently — there is no undo.

---

## 9. 🗑️ Operation 5: Destroy — Deleting the Entire List

```c
void destroy(sllnode* head);
```

This function frees every node in the list. Because all nodes were created with `malloc`, they must all be freed with `free`.

### Steps

```
a. If you've reached a NULL pointer → stop (base case)
b. Delete the rest of the list (recursive call)
c. Free the current node
```

### Why this order? — Recursion

Notice step b says "delete the rest of the list" and step c says "free the current node." This means: **free everyone else first, then free me.**

This is **recursion**. Each call to `destroy` makes the problem one node smaller by asking the next node to handle itself first.

> **Why can't you free the current node first?** Because the current node contains the `next` pointer — the only address that leads to the rest of the list. If you `free` it first, you lose that address and can never reach the remaining nodes. They become permanently orphaned (memory leak).

### Visual walkthrough — `destroy(list)` on `[12 → 15 → 9 → 13 → 10]`

**Phase 1 — Recursive calls build up (going right)**

Each `destroy()` call says: *"I won't free myself yet — let the rest of the chain go first."* This builds up stack frames:

```
destroy(12) called:
  12 is not NULL → call destroy(15) and wait...
    destroy(15) called:
      15 is not NULL → call destroy(9) and wait...
        destroy(9) called:
          9 is not NULL → call destroy(13) and wait...
            destroy(13) called:
              13 is not NULL → call destroy(10) and wait...
                destroy(10) called:
                  10 is not NULL → call destroy(NULL) and wait...
                    destroy(NULL) called:
                      NULL → STOP. Base case reached. ✅
```

Stack frames at maximum depth:
```
┌──────────────┐  ← destroy(NULL)   — base case, returns immediately
├──────────────┤  ← destroy(10)     — waiting
├──────────────┤  ← destroy(13)     — waiting
├──────────────┤  ← destroy(9)      — waiting
├──────────────┤  ← destroy(15)     — waiting
└──────────────┘  ← destroy(12)     — waiting
  STACK FRAMES
```

**Phase 2 — Stack unwinds — freeing from right to left**

Now each frame resumes where it left off (step c: free the current node):

```
destroy(NULL) returns → nothing to free

destroy(10) resumes:
  "rest of list deleted" ✓ → free node 10 → return
  List: [12 → 15 → 9 → 13]  (10 is gone)

destroy(13) resumes:
  "rest of list deleted" ✓ → free node 13 → return
  List: [12 → 15 → 9]  (13 is gone)

destroy(9) resumes:
  "rest of list deleted" ✓ → free node 9 → return
  List: [12 → 15]

destroy(15) resumes:
  "rest of list deleted" ✓ → free node 15 → return
  List: [12]

destroy(12) resumes:
  "rest of list deleted" ✓ → free node 12 → return
  List: empty ✅
```

Nodes are freed **right to left** (10, 13, 9, 15, 12) even though the traversal went left to right. This is the key insight of the recursive approach: you go all the way to the end first, then free on the way back.

### What would happen if you freed from left to right? ❌

If you called `free(head)` immediately without saving the rest:

```
free node 12 → 12 is gone, including its next pointer
→ We've lost the address of 15 → [15, 9, 13, 10] are permanently orphaned
→ Memory leak of 4 nodes ❌
```

This is why you must always free the *rest first*, then free yourself.

---

## 10. ❓ Operation 4: Delete a Single Element — Why It's Tricky

Deleting the *entire* list is handled by `destroy`. But what about deleting just *one* specific node from the middle?

This is notably tricky with singly-linked lists. Here's the problem:

```
List: [12 → 15 → 9 → 13 → 10]

Want to delete node 9.

Before:  ... → [15|→] ──► [9|→] ──► [13|→] → ...

After:   ... → [15|→] ─────────────► [13|→] → ...
                          ↑ 9 is freed and gone
```

To delete node 9, you need to:
1. Have a pointer to node 15 (the node *before* 9)
2. Set `15->next = 9->next` (make 15 point to 13)
3. Then `free(9)`

But to get a pointer to the node *before* the one you want to delete, you need to either:
- Keep **two pointers** that move through the list together — one step apart — so when the front pointer reaches the target, the back pointer is at the node before it
- Or traverse twice (once to find the position, once more to get the predecessor)

```
Two-pointer technique:
prev ──► [15|→] ──► [9|→] ──► [13|→]
                     ↑
                    curr  ← "I want to delete curr"
                              "prev must connect to curr->next"

→ prev->next = curr->next;  // skip over 9
→ free(curr);               // now safe to free 9
```

Singly-linked lists don't let you go backwards — there's no `prev` pointer in each node. You have to manually maintain two pointers as you traverse. It's manageable, but messy.

> **This is one of the key motivations for doubly-linked lists**, where each node stores *both* a `next` pointer *and* a `prev` pointer, making deletion of any single element clean and straightforward.

---

## 11. ⚖️ Arrays vs. Singly-Linked Lists — Trade-Offs

| Aspect | Array | Singly-Linked List |
|--------|-------|--------------------|
| **Memory layout** | Contiguous — all elements side by side | Scattered — elements anywhere in memory |
| **Size** | Fixed at declaration | Dynamic — grows and shrinks on demand |
| **Insert at front** | O(n) — must shift all elements | O(1) — just update two pointers |
| **Insert at end** | O(1) if space exists | O(n) — must traverse to the end first |
| **Search** | O(n) linear / O(log n) binary | O(n) linear only |
| **Random access** | ✅ O(1) — `list[5]` jumps directly | ❌ Must traverse from head |
| **Binary search** | ✅ Possible (contiguous, indexed) | ❌ Impossible (no index, no jump) |
| **Memory per element** | 4 bytes (just the int) | 12 bytes (int + pointer) |
| **Resizing** | Copy everything to a new block | Just malloc a new node |
| **Deleting one element** | O(n) — shift elements left | O(n) — traverse + two-pointer technique |
| **Deleting everything** | O(1) — just free the array block | O(n) — must free every node individually |

### When to use which?

| Situation | Best choice | Why |
|-----------|-------------|-----|
| You need random access (`list[i]`) | Array | No index in linked list |
| You need to binary search | Array | Requires random access |
| Size changes frequently and unpredictably | Linked list | No resizing cost |
| Inserting at the front often | Linked list | O(1) vs O(n) |
| Memory is very tight | Array | No pointer overhead |
| You want simple, clean code | Array | Linked list is more complex |

---

## 12. 📌 Summary

| Concept | Detail |
|---------|--------|
| **Singly-linked list** | Chain of nodes — each holds data and a pointer to the next node |
| **Node struct** | `typedef struct sllist { VALUE val; struct sllist* next; } sllnode;` |
| **Two-name struct** | `struct sllist` = temporary name (used inside); `sllnode` = permanent name (after closing brace) |
| **NULL = end of list** | The last node's `next` is always NULL |
| **Head** | Pointer to the first node — your only entry point to the whole list |
| **Never move head** | Always create a `trav` pointer for traversal; keep head fixed |
| **No malloc for trav** | `trav` is just another pointer to an existing node — no new memory needed |
| **Insert at front** | O(1) — always; connect new node FIRST, then update head |
| **Insert at end** | O(n) — must traverse to find the end |
| **Search** | O(n) — linear only; no binary search possible |
| **Delete entire list** | Recursive: delete rest first, then free self (right to left) |
| **Delete single node** | Requires two-pointer technique; trickier in singly-linked lists |
| **No random access** | Cannot jump to element N — must traverse from head every time |
| **Memory per node** | ~12 bytes (4 for int + 8 for pointer on 64-bit) vs 4 bytes in a plain array |

### The critical insertion rule

```
ALWAYS:
1. new->next = list;   // connect new node to existing list FIRST
2. list = new;         // THEN update head to new node

NEVER reverse this. Moving head first = orphaned list = memory leak.
```

### The five operations at a glance

```
create(val)             → malloc + init → return pointer to new node
find(head, val)         → trav from head → compare each node's val
insert(head, val)       → malloc + connect + update head → return new head
destroy(head)           → recurse to end → free on the way back (right to left)
delete single element   → two-pointer traverse → rewire → free (tricky in SLL)
```
