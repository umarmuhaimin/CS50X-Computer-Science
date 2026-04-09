🔗 Linked Lists (Lecture 5 — Data Structures @ CS50)

A **linked list** is a data structure that solves the biggest problem with arrays: you no longer need all your data to be in contiguous memory. Instead of forcing values to sit next to each other, linked lists **stitch separate pieces of memory together using pointers**. This gives you a data structure that can grow and shrink freely — at a cost we'll explore carefully.

---

## 1. 🧠 The Core Idea — Scattered Memory, Connected by Arrows

With arrays, all data must be side-by-side in memory. What if you can't guarantee that?

```
Memory (realistic picture):
┌────────────────────────────────────────────────────────────┐
│ ...garbage... │ 1 │ ...garbage... │ 2 │ ...garbage... │ 3 │
│               0x100               0x456               0x789 │
└────────────────────────────────────────────────────────────┘
```

These three values (1, 2, 3) are scattered anywhere in memory. With arrays, this is useless — you couldn't navigate them. But what if each value also stored the **address of the next value**?

```
┌──────────────┐      ┌──────────────┐      ┌──────────────┐
│ number: 1    │      │ number: 2    │      │ number: 3    │
│ next: 0x456  │ ───► │ next: 0x789  │ ───► │ next: NULL   │
└──────────────┘      └──────────────┘      └──────────────┘
    0x100                  0x456                  0x789
       ▲
       │
    list  (pointer to the first node — called the "head")
```

Now you can navigate from one value to the next by following the arrows (pointers). This chain of connected pieces is a **linked list**.

---

## 2. 📦 What Is a Node?

Each box in the diagram above is called a **node**. A node is a generic container that holds:
1. **Data** — the actual value you care about (e.g. an integer)
2. **Metadata** — a pointer to the next node

- **Clarification — "node":** The word "node" is just a generic term for an element in a linked data structure. Think of it like a bead on a necklace — it has a value and a hook that connects it to the next bead.

In C, a node is defined as a struct:

```c
typedef struct node
{
    int number;        // the data
    struct node *next; // pointer to the next node
}
node;
```

### Why `struct node` instead of just `node` inside the struct? ⚠️

```c
typedef struct node
{          ↑
    int number;
    struct node *next;  // ← must say "struct node", not just "node"
}
node;
```

The C compiler reads code **top to bottom**. When it reaches `struct node *next`, it hasn't finished defining the full `node` type yet (that only happens at the closing brace). So you can't use `node *next` inside the struct — the word `node` doesn't exist yet in the compiler's view.

The fix: give the struct a **temporary name** on the first line (`struct node`). Now `struct node` is already known to the compiler when it reads the `next` field inside. This is a C-specific quirk you just have to do for self-referential structs.

---

## 3. 🔧 New Operator: `->` (Arrow)

Before seeing code, you need the `->` operator.

```c
n->number    // means: go to the node that n points to, then access its "number" field
n->next      // means: go to the node that n points to, then access its "next" field
```

This is equivalent to writing `(*n).number`, but much cleaner.

| Syntax | Meaning |
|--------|---------|
| `(*n).number` | Dereference n, then access number — valid but ugly |
| `n->number` | Same thing — go to n, then get number field |

The `->` arrow in code literally matches the arrows we draw in memory diagrams.

---

## 4. 🏗️ Building a Linked List — Prepend

The simplest way to build a linked list: always insert new nodes **at the front** (prepend).

```c
// Start to build a linked list by prepending nodes
#include <cs50.h>
#include <stdio.h>
#include <stdlib.h>

typedef struct node
{
    int number;
    struct node *next;
} node;

int main(void)
{
    // Memory for numbers (list starts empty, pointing to nothing)
    node *list = NULL;

    // Build list — insert 3 nodes
    for (int i = 0; i < 3; i++)
    {
        // Allocate node for number
        node *n = malloc(sizeof(node));
        if (n == NULL)
        {
            return 1;
        }
        n->number = get_int("Number: ");
        n->next = NULL;

        // Prepend node to list
        n->next = list;  // new node points to whatever was the old first node
        list = n;        // list now starts at the new node
    }
    return 0;
}
```

### Step-by-step memory diagram (inserting 1, then 2, then 3)

**Start:** `list = NULL` (empty list)
```
list → NULL
```

**Insert 1:**
```
n → [ number:1 | next:NULL ]

Step 1: n->next = list    →  n → [ 1 | next:NULL ]
Step 2: list = n          →  list → [ 1 | next:NULL ]
```

**Insert 2:**
```
n → [ number:2 | next:? ]

Step 1: n->next = list    →  n → [ 2 | next:──────────► [ 1 | NULL ] ]
Step 2: list = n          →  list → [ 2 | ──────────► [ 1 | NULL ] ]
```

**Insert 3:**
```
n → [ number:3 | next:? ]

Step 1: n->next = list    →  n → [ 3 | ──► [ 2 | ──► [ 1 | NULL ] ] ]
Step 2: list = n          →  list → [ 3 | ──► [ 2 | ──► [ 1 | NULL ] ] ]
```

**Final result:**
```
list → [3|→] → [2|→] → [1|NULL]
```

Notice: the values appear **backwards** (3, 2, 1) because we always prepend. The most recently added value ends up at the front.

> **Why `n->next = list` before `list = n`?** Order matters critically. If you did `list = n` first, you'd lose the pointer to the old list — it would become orphaned in memory (a memory leak). Always chain the new node to the old list first, then update the head.

---

## 5. 🖨️ Traversing and Printing a Linked List

To traverse the list, use a temporary pointer that walks from node to node:

```c
// Print numbers
node *ptr = list;      // ptr starts at the head
while (ptr != NULL)    // keep going until we reach the end
{
    printf("%i\n", ptr->number);
    ptr = ptr->next;   // advance to the next node
}
```

Or equivalently, using a for loop (more compact):

```c
for (node *ptr = list; ptr != NULL; ptr = ptr->next)
{
    printf("%i\n", ptr->number);
}
```

Both do the same thing. The `while` version is more readable for beginners; the `for` version is more concise.

**Visual of traversal:**
```
ptr starts here:
list → [3|→] → [2|→] → [1|NULL]
  ↑ ptr         ↑ ptr moves    ↑ ptr moves    ptr becomes NULL → stop
```

---

## 6. 📎 Appending to the End of a Linked List

Instead of prepending (adding to the front), you can append (add to the back). This keeps the list in insertion order (1, 2, 3 instead of 3, 2, 1).

```c
// Appends numbers to a linked list
// ...same setup...
int main(void)
{
    node *list = NULL;

    for (int i = 0; i < 3; i++)
    {
        node *n = malloc(sizeof(node));
        if (n == NULL) { return 1; }
        n->number = get_int("Number: ");
        n->next = NULL;

        // If list is empty, this node IS the list
        if (list == NULL)
        {
            list = n;
        }
        else
        {
            // Walk to the end of the list
            for (node *ptr = list; ptr != NULL; ptr = ptr->next)
            {
                if (ptr->next == NULL)   // found the last node
                {
                    ptr->next = n;       // attach new node at the end
                    break;
                }
            }
        }
    }
    // ...print and free...
}
```

**Trade-off:** Appending requires walking the entire list every time to find the last node — that's O(n) per insertion. Prepending is O(1) because you only touch the front.

---

## 7. 📊 Sorted Insertion — Keeping the List in Order

You can also insert nodes so the list stays sorted, no matter the order values are given. There are exactly 4 scenarios to handle:

| Scenario | Action |
|----------|--------|
| List is empty | New node IS the list |
| New value < current head | Prepend — new node goes at front |
| New value > all existing | Append — new node goes at end |
| New value belongs in the middle | Walk until you find the right spot, then stitch in |

```c
// Implements a sorted linked list of numbers
// ...setup...
        // If list is empty
        if (list == NULL)
        {
            list = n;
        }
        // If number belongs at beginning of list
        else if (n->number < list->number)
        {
            n->next = list;
            list = n;
        }
        // If number belongs later in list
        else
        {
            for (node *ptr = list; ptr != NULL; ptr = ptr->next)
            {
                // If at end of list
                if (ptr->next == NULL)
                {
                    ptr->next = n;
                    break;
                }
                // If in middle of list
                if (n->number < ptr->next->number)
                {
                    n->next = ptr->next;
                    ptr->next = n;
                    break;
                }
            }
        }
```

**Inserting 3 in middle — visual:**
```
Before:  list → [1|→] → [2|→] → [4|NULL]
                              ↑ ptr is here

Step 1: n->next = ptr->next   →  n → [3|→] → [4|NULL]
Step 2: ptr->next = n         →  [2|→] → [3|→] → [4|NULL]

After:   list → [1|→] → [2|→] → [3|→] → [4|NULL]
```

---

## 8. 🗑️ Freeing a Linked List — The `unload` Function

You can't just `free(list)` — that only frees the first node and loses the rest (memory leak). You must free **every node one by one**, walking through the list carefully.

The tricky part: you must save a pointer to the next node **before** freeing the current one, otherwise you'd lose your path forward.

```c
void unload(node *list)
{
    node *ptr = list;
    while (ptr != NULL)
    {
        node *next = ptr->next;  // save next BEFORE freeing current
        free(ptr);               // free current node
        ptr = next;              // move to saved next
    }
}
```

**Visual:**
```
ptr → [3|→] → [2|→] → [1|NULL]

Iteration 1:
  next = ptr->next (saves address of [2])
  free(ptr)   →  [3] is freed
  ptr = next  →  ptr now points to [2]

Iteration 2:
  next = ptr->next (saves address of [1])
  free(ptr)   →  [2] is freed
  ptr = next  →  ptr now points to [1]

Iteration 3:
  next = ptr->next (NULL)
  free(ptr)   →  [1] is freed
  ptr = next  →  ptr = NULL → loop ends ✅
```

---

## 9. 🧹 When and Where to Free Memory — And Why

Every time you call `malloc`, you are **borrowing** memory from the operating system. The OS doesn't automatically take it back while your program is running. If you never return it with `free`, the memory stays claimed even after you've stopped using it — this is called a **memory leak**.

### Why does this matter?

```
Short program (runs for 1 second):
  → Memory leaks go away when the program quits. OS reclaims everything.
  → Not ideal, but not catastrophic.

Long-running program (server, database, contacts app):
  → Each iteration leaks a little memory.
  → Over hours or days, the program consumes more and more RAM.
  → Eventually the system slows down, crashes, or runs out of memory entirely.
```

For programs that run continuously — which is most real software — memory leaks are serious bugs.

---

### Rule: Free memory as soon as you are done with it

The general principle: **free memory at the latest possible moment — but no later.**

| When | What to free |
|------|-------------|
| When you `realloc` and it succeeds | Free the old block (or let `realloc` do it for you) |
| When you're done with a node | `free(node)` immediately — don't wait until the end of the program |
| When a linked list is no longer needed | Free every node using `unload` |
| Before returning an error | Free anything already allocated before quitting |
| At the end of `main` | Free any remaining dynamic memory |

---

### The dangerous patterns to avoid

**Pattern 1 — Reassigning a pointer without freeing first (memory leak)**

```c
int *list = malloc(3 * sizeof(int));
// ... use list ...

list = malloc(4 * sizeof(int));  // ❌ WRONG!
                                  // The original 3-int block is now lost forever.
                                  // You can never free it. It leaks.
```

Fix: always use a temporary variable:

```c
int *tmp = malloc(4 * sizeof(int));
if (tmp != NULL)
{
    free(list);  // ✅ return old memory first
    list = tmp;  // then point list at the new block
}
```

**Pattern 2 — Freeing a linked list node by node but in the wrong order**

```c
// ❌ WRONG — frees node but then tries to follow its pointer
node *ptr = list;
while (ptr != NULL)
{
    free(ptr);         // node is freed and gone
    ptr = ptr->next;   // ❌ accessing freed memory — undefined behaviour!
}
```

Fix: save `next` before freeing:

```c
// ✅ CORRECT — always save next before freeing
node *ptr = list;
while (ptr != NULL)
{
    node *next = ptr->next;  // save where to go next
    free(ptr);               // now safe to free
    ptr = next;              // move forward using the saved pointer
}
```

**Pattern 3 — Forgetting to free before aborting on error**

```c
node *n = malloc(sizeof(node));
if (n == NULL)
{
    return 1;  // ❌ forgot to free already-allocated memory before exiting
}

// Correct version — if you allocated earlier nodes in a loop, free them first:
if (n == NULL)
{
    unload(list);  // ✅ free everything built so far
    return 1;
}
```

---

### Where in the code should `free` appear?

```
malloc appears here         free appears here
      ↓                           ↓
node *n = malloc(...)    →   free(n);         ← when node is no longer needed

In a linked list:
  malloc for each node     →   unload(list)   ← when the entire list is done

In main with realloc:
  malloc initial list      →   free(list)     ← at the very end of main
```

A useful mental model: think of `malloc` and `free` as a **matching pair** — every `malloc` must have exactly one `free` for the same address. Too many `free` calls on the same address = crash ("double free"). Too few = memory leak.

---

### Checking for leaks — Valgrind

The tool `valgrind` can detect memory leaks automatically:

```bash
valgrind ./your_program
```

It will report:
- **"All heap blocks were freed"** — no leaks ✅
- **"definitely lost: X bytes"** — you forgot to free something ❌
- **"Invalid read/write"** — you accessed freed or out-of-bounds memory ❌

---

## 10. ⚖️ Complexity Analysis

| Operation | Time Complexity | Reason |
|-----------|----------------|--------|
| **Insert (prepend)** | O(1) | Only touch the front — constant steps no matter the list size |
| **Insert (append)** | O(n) | Must walk to the end first |
| **Insert (sorted)** | O(n) | Must walk to find the right position |
| **Search** | O(n) | Must traverse from the beginning — no jumping to middle |
| **Delete** | O(n) | Must find the node first |
| **Traversal (print)** | O(n) | Must visit every node |

### Why can't you do binary search? ⚠️

Binary search requires jumping to the **middle** of the list in one step. With an array, you calculate `middle = (low + high) / 2` and jump directly to that index.

With a linked list, there's no way to jump. You must start at the head and count your way forward. To find the middle of a 1000-node list, you'd traverse 500 nodes first. Then to find the middle of that half, 250 more. This degrades binary search from O(log n) to O(n) — making it useless.

---

## 11. 🔄 Linked Lists vs. Arrays

| Aspect | Array | Linked List |
|--------|-------|-------------|
| **Memory layout** | Contiguous — all together | Scattered — anywhere in memory |
| **Resizing** | Must copy everything | Just add a new node — no copying |
| **Access by index** | O(1) — direct jump | O(n) — must traverse from head |
| **Binary search** | ✅ O(log n) | ❌ Not possible |
| **Insert at front** | O(n) — shift everything right | O(1) — just update pointers |
| **Memory overhead** | Low — only the data | Higher — data + pointer per node |
| **Flexibility** | Fixed size (without realloc) | Grows/shrinks freely |

> **The trade-off in plain English:** Arrays are fast to search but slow to grow. Linked lists are slow to search but fast to grow. Choosing between them depends on what your program needs to do more often.

---

## 12. 📌 Summary

| Concept | Detail |
|---------|--------|
| **Node** | A struct holding data + a pointer to the next node |
| **Head (list)** | A pointer to the first node in the list |
| **`->`** | Go to the node a pointer points to, then access a field |
| **Prepend** | Insert at front — O(1) |
| **Append** | Insert at back — O(n) |
| **Traversal** | Walk with a `ptr` from head to NULL |
| **Free a list** | Must free every node individually — save `next` before `free` |
| **Can binary search?** | ❌ No — cannot jump to middle |
| **Search complexity** | O(n) — linear |
| **Why use linked list?** | Dynamic size — add/remove without copying |
| **Why not always use it?** | Slower search, more memory per element, no binary search |
