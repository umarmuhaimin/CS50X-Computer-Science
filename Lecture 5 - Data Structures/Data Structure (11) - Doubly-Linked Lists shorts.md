↔️ Doubly-Linked Lists (Lecture 5 — Data Structures @ CS50 | Doug Lloyd)

A doubly-linked list is a direct evolution of the singly-linked list — it fixes the one critical limitation that singly-linked lists have by adding a single extra pointer to every node. The result is a structure that can traverse in both directions, making operations like deleting a single node clean and efficient instead of messy and error-prone.

---

## 1. 🧠 The Problem — Why Singly-Linked Lists Fall Short

Recall from the singly-linked list (SLL) short: when you needed to delete a single node from the middle of an SLL, you needed to find the node *before* the target — because you had to update its `next` pointer. But in an SLL, you can only move **forward**. There's no way to look backward.

The two solutions in an SLL were both awkward:
- Keep two pointers moving together (one step apart), so when the front reaches the target, the back is at the predecessor
- Traverse twice — once to find the position, once more to get the predecessor

Both work, but both are messy. And neither scales gracefully.

```
SLL — to delete node 13:

list ──► [12|→] ──► [15|→] ──► [9|→] ──► [13|→] ──► [10|NULL]
                                 ↑
                          You need to be HERE (at 9)
                          to fix the chain when deleting 13.
                          But if you're AT 13, you can't go back to 9.
```

**The root cause:** Each node in an SLL only knows where the *next* node is. It has no memory of where it came from.

---

## 2. ↔️ What Is a Doubly-Linked List?

A doubly-linked list (DLL) solves this by giving every node a second pointer — one pointing **forward** (`next`) and one pointing **backward** (`prev`). Now every node knows both its predecessor and its successor.

```
Singly-linked list (one direction only):
list ──► [ val | next ──► ] ──► [ val | next ──► ] ──► [ val | NULL ]

Doubly-linked list (both directions):
list ──► [ NULL ◄── prev | val | next ──► ] ◄──► [ prev | val | next ──► ] ◄──► [ prev | val | NULL ]
```

Visual of a DLL node (three fields):

```
┌──────────────────┐
│       prev       │  ← address of the PREVIOUS node (or NULL if first)
├──────────────────┤
│       val        │  ← the actual data
├──────────────────┤
│       next       │  ← address of the NEXT node (or NULL if last)
└──────────────────┘
```

A full DLL with four nodes `[15 → 9 → 13 → 10]`:

```
         list
          │
          ▼
┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  [NULL]     │◄────│  prev       │◄────│  prev       │◄────│  prev       │
│    15       │     │    9        │     │    13       │     │    10       │
│  [next] ───►│────►│  [next] ───►│────►│  [next] ───►│────►│  [NULL]     │
└─────────────┘     └─────────────┘     └─────────────┘     └─────────────┘
```

- The first node's `prev` is `NULL` — nothing comes before it.
- The last node's `next` is `NULL` — nothing comes after it.
- Every middle node's `prev` and `next` both hold valid addresses.

---

## 3. 🏗️ The Node Struct — One Extra Field

The only change from an SLL node is adding `struct dllist* prev`:

```c
// Singly-linked list node (2 fields)
typedef struct sllist
{
    VALUE val;
    struct sllist* next;
}
sllnode;

// Doubly-linked list node (3 fields) — just add prev!
typedef struct dllist
{
    VALUE val;
    struct dllist* prev;   // ← the new field
    struct dllist* next;
}
dllnode;
```

That's literally the only structural difference. One extra pointer per node.

### The memory cost

Each pointer on a 64-bit system is 8 bytes. So each DLL node is 8 bytes larger than its SLL equivalent:

| Node type | Fields | Memory |
|-----------|--------|--------|
| SLL node | val (4B) + next (8B) | ~12 bytes |
| DLL node | val (4B) + prev (8B) + next (8B) | ~20 bytes |

> **Is the extra memory worth it?** Only if you need to delete single elements from the middle of the list. If you're only ever inserting and searching (no single-node deletion), an SLL wastes less memory and is simpler. If deletion is a core operation, DLL pays off immediately.

---

## 4. 📋 The 5 Operations — What Changes vs. SLL

| Operation | Change in DLL vs SLL |
|-----------|---------------------|
| **Create** | Nearly identical — just initialise `prev = NULL` as well |
| **Search (find)** | Identical — still traverse forward from head to NULL |
| **Insert** | Extra step: fix the old head's `prev` to point back to the new node |
| **Delete single node** | Now clean and O(1) — the killer feature of DLL |
| **Destroy (entire list)** | Nearly identical — still recurse/iterate and free each node |

Only Insert and Delete are meaningfully different. The other three are minor tweaks covered by the SLL notes.

---

## 5. 📥 Operation: Insert — Adding a Node at the Front

```c
dllnode* insert(dllnode* head, VALUE val);
```

### Steps

```
a. Dynamically allocate space for a new dllnode
b. Check to make sure we didn't run out of memory
c. Populate and insert the node at the beginning of the linked list
d. Fix the prev pointer of the old head of the linked list ← NEW step
e. Return a pointer to the new head of the linked list
```

Step d is the only addition compared to SLL insertion. Because nodes now know their predecessors, the old head must update its `prev` to point back to the new node.

### Visual walkthrough — `list = insert(list, 12)` on `[15 → 9 → 13 → 10]`

**Starting state:**
```
         list
          │
          ▼
┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  [NULL]     │◄────│  prev       │◄────│  prev       │◄────│  prev       │
│    15       │     │    9        │     │    13       │     │    10       │
│  [next] ───►│────►│  [next] ───►│────►│  [next] ───►│────►│  [NULL]     │
└─────────────┘     └─────────────┘     └─────────────┘     └─────────────┘
```

**Step a+b — malloc node 12, check for NULL:**
```
┌─────────────┐
│      ?      │  ← new node (garbage values)
│     12      │
│      ?      │
└─────────────┘
      ↑
     new
```

**Step c — Fill new node's OWN pointers FIRST (before touching anyone else):**

What comes after 12? The old head, 15 → set `new->next = list`
What comes before 12? Nothing → set `new->prev = NULL`

```
┌─────────────┐
│   [NULL]    │  ← prev = NULL (12 is the new head, nothing before it)
│     12      │
│  [next] ───►│──────────────────────────────────────────────────────────►
└─────────────┘                                                           │
      ↑                                                                   │
     new                                                                  ▼
                                                              ┌─────────────┐
                                                              │  [NULL]     │◄────...
                                                              │    15       │
                                                              │  [next] ───►│────►...
                                                              └─────────────┘
                                                                    ↑
                                                                   list (still here)
```

**Step d — Fix old head's `prev` to point BACK to new node:**

Node 15 currently thinks nothing comes before it (`prev = NULL`). That's now wrong — node 12 comes before it. Update `list->prev = new`:

```
     new                          list
      │                            │
      ▼                            ▼
┌─────────────┐          ┌─────────────┐
│   [NULL]    │◄─────────│  prev       │  ← 15->prev now points back to 12
│     12      │          │    15       │
│  [next] ───►│─────────►│  [next] ───►│──► ...
└─────────────┘          └─────────────┘
```

**Step e — Return new node as the new head (`list = new`):**

```
         list
          │
          ▼
┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   [NULL]    │◄────│  prev       │◄────│  prev       │◄────│  prev       │◄────│  prev       │
│     12      │     │    15       │     │    9        │     │    13       │     │    10       │
│  [next] ───►│────►│  [next] ───►│────►│  [next] ───►│────►│  [next] ───►│────►│  [NULL]     │
└─────────────┘     └─────────────┘     └─────────────┘     └─────────────┘     └─────────────┘
```

List is now `[12 ↔ 15 ↔ 9 ↔ 13 ↔ 10]`. ✅

### The recommended insertion order

> **Fill the NEW node's pointers first, then update the old head's `prev`, then move `list`.**
> Never touch someone else's pointers until your new node is fully connected. Having redundant pointers temporarily is completely fine — they will be overwritten. What's NOT fine is breaking the chain.

```c
// Correct insertion order:
new->val  = val;       // 1. set the value
new->prev = NULL;      // 2. new node's prev (nothing before it)
new->next = head;      // 3. new node's next (old head)
if (head != NULL)
    head->prev = new;  // 4. old head's prev now points back to new node
return new;            // 5. new node becomes the new head
```

---

## 6. 🗑️ Operation: Delete a Single Node — The DLL Advantage

```c
void delete(dllnode* target);
```

This is where DLL truly shines. In an SLL, deleting a single node required the awkward two-pointer trick. In a DLL, it's clean and simple — just two pointer reassignments and one `free`.

> **Key insight:** You're given a direct pointer to the node you want to delete (`target`). Because it has a `prev` pointer, you can immediately access the node before it without any extra traversal.

### Steps

```
a. Fix the pointers of the surrounding nodes to "skip over" target
b. Free target
```

That's it. Two steps.

### The three rewiring lines

```c
target->prev->next = target->next;   // the node before target now skips to the node after target
target->next->prev = target->prev;   // the node after target now points back to the node before target
free(target);                         // target is now safely removed — free it
```

### Visual walkthrough — `delete(x)` where x points to node 13

**Starting state — list `[12 ↔ 15 ↔ 9 ↔ 13 ↔ 10]`, x points to node 13:**

```
         list                                        x
          │                                          │
          ▼                                          ▼
┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   [NULL]    │◄────│  prev       │◄────│  prev       │◄────│  prev       │◄────│  prev       │
│     12      │     │    15       │     │    9        │     │    13       │     │    10       │
│  [next] ───►│────►│  [next] ───►│────►│  [next] ───►│────►│  [next] ───►│────►│  [NULL]     │
└─────────────┘     └─────────────┘     └─────────────┘     └─────────────┘     └─────────────┘
                                              ↑                   ↑
                                        x->prev (9)           target (13)
                                                                   ↓
                                                            x->next (10)
```

**Step a1 — Fix node 9's `next` to skip over 13, pointing directly to 10:**

`target->prev->next = target->next`
= "9's next = 13's next" = "9's next = 10"

```
node 9's next now jumps over 13 → points to 10
[9 | next ──────────────────────────────────────────► 10]   (skipping 13)
```

**Step a2 — Fix node 10's `prev` to skip back over 13, pointing directly to 9:**

`target->next->prev = target->prev`
= "10's prev = 13's prev" = "10's prev = 9"

```
node 10's prev now jumps back over 13 → points to 9
[9 ◄──────────────────────────────────────────── prev | 10]   (skipping 13)
```

**After both reassignments — 13 is surrounded but bypassed:**

```
         list
          │
          ▼
┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   [NULL]    │◄────│  prev       │◄────│  prev       │◄────│  prev       │
│     12      │     │    15       │     │    9        │     │    10       │
│  [next] ───►│────►│  [next] ───►│────►│  [next] ───────────────────────►│
└─────────────┘     └─────────────┘     └─────────────┘     └─────────────┘
```

Node 13 still exists in memory but nothing points to it and it points to nothing that anyone cares about — it's completely isolated.

**Step b — `free(target)` — node 13 is freed:**

```
         list
          │
          ▼
┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   [NULL]    │◄────│  prev       │◄────│  prev       │◄────│  prev       │
│     12      │     │    15       │     │    9        │     │    10       │
│  [next] ───►│────►│  [next] ───►│────►│  [next] ───►│────►│  [NULL]     │
└─────────────┘     └─────────────┘     └─────────────┘     └─────────────┘

13 is gone. List is now [12 ↔ 15 ↔ 9 ↔ 10]. ✅
```

### Why this is so much cleaner than SLL

In an SLL, to delete node 13 you needed to already have a pointer to node 9. If you only had a pointer to 13, you had to restart from the head and walk forward until you found 9. That's O(n).

In a DLL, `target->prev` *is* node 9. You're already there, instantly. No traversal needed. The deletion is always O(1) — constant time, regardless of where in the list the node is.

### ⚠️ Edge cases: head and tail deletion

The walkthrough above handles the **general case** — deleting a node that has both a valid `prev` and a valid `next` (a middle node). Edge cases need special handling:

| Case | Extra care needed |
|------|-------------------|
| **Deleting the head** | `target->prev` is NULL — you can't do `target->prev->next = ...`. Must update `list` itself to point to `target->next` |
| **Deleting the tail** | `target->next` is NULL — you can't do `target->next->prev = ...`. Must set `target->prev->next = NULL` |
| **Only one node in list** | Both `prev` and `next` are NULL. Just free it and set `list = NULL` |

Always check for `NULL` before dereferencing:
```c
if (target->prev != NULL)
    target->prev->next = target->next;

if (target->next != NULL)
    target->next->prev = target->prev;

free(target);
```

---

## 7. ⏱️ Efficiency — Constant Operations, Linear Search

The headline claim: both insertion and deletion in linked lists (SLL or DLL) run in **O(1) constant time**.

### Why insertion is O(1)

We always insert at the front. No matter how long the list is, inserting at the front takes the same number of pointer reassignments — 3 for SLL, 4 for DLL. It never changes. That's O(1).

Compare to array insertion: inserting in the middle of an array requires shifting all elements after the insertion point — that's O(n). Linked lists win here.

### Why deletion is O(1)

Assuming you already have a pointer to the node you want to delete (which is the typical case — you found it during a search), deleting it takes exactly 3 operations:
1. Fix the predecessor's `next`
2. Fix the successor's `prev`
3. `free` the node

Always 3 operations. Always O(1). It doesn't matter if the list has 10 nodes or 10 million.

### The trade-off — search is O(n)

The cost for all this insertion/deletion efficiency: **no random access**.

| Array | Linked list (SLL or DLL) |
|-------|--------------------------|
| `arr[5]` — jump directly to element 5 | Must start at head and count forward |
| O(1) access | O(n) access |
| Index-based | Pointer-based traversal only |

With an array, "give me element 10" is instant — you calculate the memory address with arithmetic. With a linked list, you must start at the head and follow `next` pointers until you count to 10. If the list has a million elements and you want the last one, that's a million steps.

> **The rule of thumb:** If your program inserts/deletes frequently → linked list wins. If your program searches/accesses frequently → array wins.

---

## 8. ⚖️ SLL vs DLL vs Array — Full Comparison

| Aspect | Array | Singly-Linked List | Doubly-Linked List |
|--------|-------|-------------------|-------------------|
| **Memory per element** | 4 bytes (just data) | ~12 bytes (data + 1 ptr) | ~20 bytes (data + 2 ptrs) |
| **Insert at front** | O(n) — shift all | O(1) | O(1) |
| **Delete single node** | O(n) — shift all | O(n) — need predecessor | **O(1)** — has prev pointer |
| **Search** | O(n) linear / O(log n) binary | O(n) linear only | O(n) linear only |
| **Random access** | ✅ O(1) | ❌ No | ❌ No |
| **Traverse backward** | ✅ (decrement index) | ❌ No | ✅ Yes |
| **Fixed size?** | ✅ Yes (without realloc) | ❌ Dynamic | ❌ Dynamic |
| **When to choose** | Frequent search/access | Insert/delete at ends only | Insert anywhere + delete single nodes |

### When to choose DLL over SLL

| Scenario | SLL or DLL? |
|----------|-------------|
| Only inserting at front, no single-node deletion | SLL — simpler, less memory |
| Need to delete specific nodes efficiently | DLL — the whole point |
| Need to traverse both forward and backward | DLL |
| Memory is extremely tight | SLL — saves 8 bytes per node |
| Implementing a queue or browser history | DLL — supports both ends efficiently |

---

## 9. 📌 Summary

| Concept | Detail |
|---------|--------|
| **DLL vs SLL difference** | One extra `prev` pointer per node — enables backward traversal |
| **DLL node struct** | `VALUE val; struct dllist* prev; struct dllist* next;` |
| **Why DLL exists** | Makes single-node deletion clean and O(1) |
| **Memory cost** | ~20 bytes per node vs ~12 in SLL — only worth it if you need single-node deletion |
| **Insert steps** | malloc → fill new node (next+prev) → fix old head's prev → update head |
| **Key insert rule** | Fill the NEW node's pointers FIRST, then fix others — never break the chain |
| **Delete steps** | `target->prev->next = target->next` → `target->next->prev = target->prev` → `free(target)` |
| **Delete complexity** | O(1) — 3 operations always, regardless of list size |
| **Insert complexity** | O(1) — always inserting at the front |
| **Search complexity** | O(n) — no random access; must traverse from head |
| **Edge cases for delete** | Check for NULL before dereferencing `prev` and `next` (head/tail deletion) |
| **DLL vs array** | DLL: O(1) insert/delete, O(n) search. Array: O(n) insert/delete, O(1) search |

### The delete operation in three lines

```c
target->prev->next = target->next;   // predecessor skips over target
target->next->prev = target->prev;   // successor skips back over target
free(target);                         // target safely removed
```

### The full node struct

```c
typedef struct dllist
{
    VALUE val;            // the data
    struct dllist* prev;  // pointer to previous node (or NULL if first)
    struct dllist* next;  // pointer to next node (or NULL if last)
}
dllnode;
```
