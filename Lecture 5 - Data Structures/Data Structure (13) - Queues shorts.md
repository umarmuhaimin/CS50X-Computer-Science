🚶 Queues — Data Structure (Lecture 5 — Data Structures @ CS50 | Doug Lloyd)

A queue is the mirror image of a stack. Both are abstract data structures with only two legal operations, but where a stack is LIFO (last in, first out), a queue is **FIFO** — first in, first out. Think of any real-world queue: the person who waited longest gets served first. That fairness principle is exactly what a queue enforces in code.

---

## 1. 🧠 What Is a Queue?

A queue is a data structure that maintains data in an organised way with one strict rule:

> **New elements are added to the END. Only the oldest element — the one at the FRONT — can be removed.**

### The bank queue analogy

Imagine a queue of people waiting at a bank:

```
FRONT (served first)          BACK (joined last)
  │                                │
  ▼                                ▼
[Person A] → [Person B] → [Person C] → [Person D]
  ↑                                        ↑
  Joined first,                       Joined last,
  leaves first                        leaves last
  (FIFO)
```

Person A has been waiting the longest and is served first. If the bank operated like a stack (LIFO), Person D would always get served immediately while Person A waited forever — deeply unfair. Queues enforce fairness by serving whoever has been waiting longest.

> **FIFO — First In, First Out**

The first element added is the first element removed.

### Real-world examples

| Example | Why it's a queue |
|---------|-----------------|
| Bank teller line | First customer to arrive is first served |
| Print queue | First document sent to printer is first printed |
| Keyboard buffer | Keys pressed first are processed first |
| CPU task scheduler | Processes submitted first run first (in basic scheduling) |
| Breadth-first search (BFS) | Nodes discovered first are explored first |

---

## 2. 📋 The Two Operations — Enqueue and Dequeue

Only two operations are legally permitted on a queue:

| Operation | What it does | Analogy |
|-----------|-------------|---------|
| **Enqueue** | Add a new element to the **end** of the queue | Person joins the back of the line |
| **Dequeue** | Remove and return the **oldest** element from the **front** | Person at the front is served and leaves |

### Stack vs Queue at a glance

| | Stack (LIFO) | Queue (FIFO) |
|--|-------------|-------------|
| **Add to** | Top | End (back) |
| **Remove from** | Top | Front |
| **Add operation** | Push | Enqueue |
| **Remove operation** | Pop | Dequeue |
| **Who leaves first** | Most recently added | Longest waiting |

---

## 3. 🔧 Array-Based Implementation — The Struct

A queue implemented with an array needs **three** fields — one more than a stack:

```c
typedef struct _queue
{
    VALUE array[CAPACITY];   // the data storage array
    int front;               // index of the oldest element (next to dequeue)
    int size;                // number of elements currently in the queue
}
queue;
```

### Why three fields instead of two?

A stack only needs `array` + `top` because both push and pop happen at the same end.

A queue adds to one end and removes from the other — so you must independently track:
- **Where is the front** (oldest element, ready to dequeue)?
- **How many elements are in the queue** (so you can calculate where to enqueue next)?

Notice: there is **no `end` field**. The location of the end is always calculated as: `front + size`.

| Field | Colour in slides | Meaning |
|-------|-----------------|---------|
| `array[CAPACITY]` | Blue | Stores the actual data |
| `front` | Green | Index of the oldest element (dequeue from here) |
| `size` | Red | Count of current elements |

### What the queue looks like in memory

```
queue q:
┌────┬────┬────┬────┬────┬────┬────┬────┬────┬────┐
│    │    │    │    │    │    │    │    │    │    │   ← array[10]
├────┴────┴────┴────┴────┴────┴────┴────┴────┴────┘
│    │                                               ← front (green)
├────┤
│    │                                               ← size (red)
└────┘
q
```

### Initialising the queue

```c
queue q;
q.front = 0;    // oldest element is at index 0
q.size  = 0;    // queue is empty
```

```
Array: [    |    |    |    |    |    |    |    |    |    ]
         0    1    2    3    4    5    6    7    8    9
front = 0  (green)
size  = 0  (red)
```

---

## 4. ➕ Enqueue — Array-Based

```c
void enqueue(queue* q, VALUE data);
```

### What enqueue does

```
a. Place data at array[front + size]   ← always the next open slot
b. Increment size                       ← one more element in the queue
c. front stays unchanged               ← oldest element hasn't moved
```

### Why is the enqueue location `front + size`?

- `front` tells you where the oldest element is
- `size` tells you how many elements exist after that
- Together, `front + size` always points to the next available slot

### Step-by-step walkthrough — enqueue(28), enqueue(33), enqueue(19)

**Start:** `q.front = 0, q.size = 0`
```
Array: [    |    |    |    |    |    |    |    |    |    ]
front = 0 | size = 0
```

---

**`enqueue(&q, 28);`** — slot = front + size = 0 + 0 = 0 → place 28 at array[0], size → 1

```
Array: [ 28 |    |    |    |    |    |    |    |    |    ]
         ↑
       front(0) stays here — 28 is the oldest element
front = 0 | size = 1
```

---

**`enqueue(&q, 33);`** — slot = 0 + 1 = 1 → place 33 at array[1], size → 2

```
Array: [ 28 | 33 |    |    |    |    |    |    |    |    ]
         ↑
       front(0) — 28 is still oldest
front = 0 | size = 2
```

---

**`enqueue(&q, 19);`** — slot = 0 + 2 = 2 → place 19 at array[2], size → 3

```
Array: [ 28 | 33 | 19 |    |    |    |    |    |    |    ]
         ↑
       front(0) — 28 is still oldest
front = 0 | size = 3
```

The queue now conceptually looks like:

```
FRONT (oldest, dequeue next)         BACK (newest)
  │                                     │
  ▼                                     ▼
[28] → [33] → [19]
```

Notice how `front` **never changed**. It always points to the oldest element. Only `size` incremented with each enqueue.

---

## 5. ➖ Dequeue — Array-Based

```c
VALUE dequeue(queue* q);
```

### What dequeue does

```
a. Save array[front]   ← the oldest element (FIFO — this one leaves first)
b. Increment front     ← the next oldest element is now at front+1
c. Decrement size      ← one fewer element in the queue
d. Return saved value  ← give the caller what they asked for
```

> **The data is not erased** — just like popping from a stack array, the old value stays in memory but is ignored. `front` moving forward means we've logically "forgotten" the dequeued element.

### Step-by-step walkthrough — dequeue sequence

**Starting state:** `array = [28, 33, 19, ...]`, `front = 0`, `size = 3`

---

**`int x = dequeue(&q);`** — dequeue 28 (FIFO: first in, first out)

```
Step 1: save array[front] = array[0] = 28  →  x = 28
Step 2: front++ → front = 1   (next oldest is 33)
Step 3: size--  → size = 2

Array: [ 28 | 33 | 19 |    |    | ... ]   ← 28 still in memory, IGNORED
         ↑         ↑
       (old       front now here (33 is oldest)
        front,
        ignored)
front = 1 | size = 2    x = 28
```

---

**`int x = dequeue(&q);`** — dequeue 33

```
Step 1: save array[1] = 33  →  x = 33
Step 2: front = 2
Step 3: size = 1

Array: [ 28 | 33 | 19 |    |    | ... ]   ← 28 and 33 ignored
                   ↑
                front now here (19 is oldest)
front = 2 | size = 1    x = 33
```

---

**`enqueue(&q, 40);`** — slot = front + size = 2 + 1 = 3 → place 40 at array[3], size → 2

```
Array: [ 28 | 33 | 19 | 40 |    |    | ... ]
                   ↑         ↑
                 front(2)  new element goes here
front = 2 | size = 2
```

Notice: slots 0 and 1 (where 28 and 33 were) are now technically empty/reclaimable.

---

## 6. 🔄 The Wrap-Around Trick — Modulo Arithmetic

Because elements are dequeued from the front and the `front` index keeps advancing, the array behaves like a **circular buffer**. Eventually, `front + size` could exceed `CAPACITY`.

### The problem

```
After many enqueue/dequeue cycles:
Array: [    |    |    |    |    |    |    |    | 19 | 40 ]
                                                  ↑
                                               front = 8
                                               size = 2

If we enqueue again: front + size = 8 + 2 = 10 → OUT OF BOUNDS! ❌
But slots 0-7 are empty and available!
```

### The solution — modulo by CAPACITY

```c
// When enqueueing, the correct slot is:
array[(front + size) % CAPACITY]
```

```
(8 + 2) % 10 = 10 % 10 = 0   → wraps around to slot 0 ✅
(8 + 3) % 10 = 11 % 10 = 1   → wraps around to slot 1 ✅
```

This makes the array behave as a **ring** — elements fill forward until the end, then wrap to the beginning, reusing the slots freed by dequeuing.

### How do you know the queue is full?

```c
if (q.size == CAPACITY)  // queue is full — no more room to enqueue
```

- **Clarification — why `size` not `front`?** `front` keeps advancing and could be at any index. `size` directly tells you how many real elements exist. When `size == CAPACITY`, every slot is occupied regardless of where `front` is.

---

## 7. 🔗 Linked List-Based Implementation — The Struct

A queue can also be implemented with a linked list. Unlike the stack (which used a singly-linked list), Doug Lloyd uses a **doubly-linked list** here because the queue needs efficient access to both ends:

```c
typedef struct _queue
{
    VALUE val;               // the data
    struct _queue *prev;     // pointer to the previous node (back toward head)
    struct _queue *next;     // pointer to the next node (forward toward tail)
}
queue;
```

- **Clarification — why doubly-linked?** A singly-linked list *can* work for a queue, but a doubly-linked list makes dequeue cleaner: the new head can set its `prev` to NULL directly, without any extra tricks. It also models the concept more naturally since a queue has two distinct ends.

### The critical rule: maintain BOTH head and tail ⚠️

```
head ──────────────────────────── tail
  │                                 │
  ▼                                 ▼
[12|→] ↔ [15|→] ↔ [9|→] ↔ [13|→]

head = pointer to oldest element   (dequeue from here)
tail = pointer to newest element   (enqueue here)
```

Unlike a stack where you only need the head (because both push and pop happen at the front), a queue needs:
- **`head`** — to dequeue the oldest element
- **`tail`** — to enqueue new elements at the back without traversing the whole list

Both are typically global variables.

### DLL node visualised

```
┌─────────────┐
│  prev  ◄────│  ← address of node before this one (or NULL if head)
├─────────────┤
│    val      │  ← the actual data
├─────────────┤
│  next  ────►│  ← address of node after this one (or NULL if tail)
└─────────────┘
```

---

## 8. ➕ Enqueue — Linked List-Based

Add a new node to the **tail** (back) of the list.

### Steps

```
a. Dynamically allocate (malloc) a new node
b. Check for NULL — malloc can fail
c. Set new->val = data
d. Set new->prev = tail     (new node looks back to the current tail)
e. Set new->next = NULL     (new node is the last — nothing comes after it)
f. Set tail->next = new     (old tail now points forward to new node)
g. Move tail = new          (new node is now the tail)
```

### Visual walkthrough — `enqueue(tail, 10)` on `[12 ↔ 15 ↔ 9 ↔ 13]`

**Starting state:**
```
head                              tail
 │                                 │
 ▼                                 ▼
[NULL|12|→] ↔ [←|15|→] ↔ [←|9|→] ↔ [←|13|NULL]
```

**Step a+b — malloc new node:**
```
new ──► [ ? | ? | ? ]   ← garbage values
```

**Step c — set val = 10:**
```
new ──► [ ? | 10 | ? ]
```

**Step d — `new->prev = tail` (point back to node 13):**
```
new ──► [ ←13 | 10 | ? ]   (new looks back at 13)
```

**Step e — `new->next = NULL` (new is the last node):**
```
new ──► [ ←13 | 10 | NULL ]
```

**Step f — `tail->next = new` (old tail 13 now points forward to new node):**
```
head                              tail           new
 │                                 │              │
 ▼                                 ▼              ▼
[NULL|12|→] ↔ [←|15|→] ↔ [←|9|→] ↔ [←|13|──────►|←13|10|NULL]
```

**Step g — `tail = new` (new node becomes the new tail):**
```
head                                             tail
 │                                                │
 ▼                                                ▼
[NULL|12|→] ↔ [←|15|→] ↔ [←|9|→] ↔ [←|13|→] ↔ [←|10|NULL]
```

10 is now the last element in the queue. ✅

---

## 9. ➖ Dequeue — Linked List-Based

Remove the node at the **head** (front) of the list.

### Steps

```
a. Create trav pointer pointing to head->next (the second element)
b. Free the head node
c. Set trav->prev = NULL    (the new head has no predecessor)
d. Move head = trav         (second element becomes new head)
e. Return the dequeued value
```

### Visual walkthrough — `dequeue(head)` on `[12 ↔ 15 ↔ 9 ↔ 13 ↔ 10]`

**Starting state:**
```
head                                             tail
 │                                                │
 ▼                                                ▼
[NULL|12|→] ↔ [←|15|→] ↔ [←|9|→] ↔ [←|13|→] ↔ [←|10|NULL]
```

**Step a — `trav = head->next` (get a handle on the second element, 15):**
```
head           trav
 │              │
 ▼              ▼
[NULL|12|→] ↔ [←|15|→] ↔ [←|9|→] ↔ [←|13|→] ↔ [←|10|NULL]
```

**Step b — `free(head)` — node 12 is removed:**
```
[FREED]        trav                              tail
               │                                  │
               ▼                                  ▼
             [←|15|→] ↔ [←|9|→] ↔ [←|13|→] ↔ [←|10|NULL]
               ↑
        (still has prev pointing to freed space — we fix this next)
```

**Step c — `trav->prev = NULL` (15 is the new head, nothing comes before it):**
```
[NULL|15|→] ↔ [←|9|→] ↔ [←|13|→] ↔ [←|10|NULL]
  ↑
trav (and soon head)
```

**Step d — `head = trav` (15 is now the official head):**
```
head                                             tail
 │                                                │
 ▼                                                ▼
[NULL|15|→] ↔ [←|9|→] ↔ [←|13|→] ↔ [←|10|NULL]
```

12 has been dequeued. Queue is now `[15 ↔ 9 ↔ 13 ↔ 10]`. ✅

The returned value is 12 — the former oldest element.

---

## 10. ⚖️ Array vs Linked List Implementation

| Aspect | Array-Based Queue | Linked List-Based Queue |
|--------|------------------|------------------------|
| **Max size** | Fixed at `CAPACITY` | Unlimited (grows dynamically) |
| **Struct fields** | `array` + `front` + `size` | DLL node: `val` + `prev` + `next` |
| **External pointers needed** | None (all in struct) | `head` + `tail` (global) |
| **Enqueue location** | `array[(front + size) % CAPACITY]` | Always at `tail` |
| **Dequeue location** | `array[front]` | Always from `head` |
| **Wrap-around** | ✅ Needed — use modulo | ❌ Not needed — list grows |
| **Overflow risk** | ✅ Yes — `size == CAPACITY` | ❌ No — limited only by RAM |
| **Memory** | Pre-allocated, slots reused | Per-node, never wasted |
| **Code complexity** | Medium (modulo logic) | Higher (malloc + 2 pointers) |

---

## 11. ⚖️ Stack vs Queue — When to Use Which

| Situation | Use | Reason |
|-----------|-----|--------|
| Undo/redo in editors | Stack | Most recent action undone first (LIFO) |
| Browser back button | Stack | Last visited page is first to go back to |
| Function call tracking | Stack | Most recent call returns first |
| Print jobs | Queue | First document submitted is printed first (fairness) |
| Keyboard input buffer | Queue | Keys processed in the order pressed (FIFO) |
| Breadth-first search (BFS) | Queue | Explore nearest nodes first, then further ones |
| Task scheduling (fair) | Queue | Oldest task runs next |
| Reversing a sequence | Stack | Push all, then pop — output is reversed |

---

## 12. 📌 Summary

| Concept | Detail |
|---------|--------|
| **Queue** | FIFO data structure — First In, First Out |
| **Enqueue** | Add to the **end** (back) of the queue |
| **Dequeue** | Remove from the **front** of the queue |
| **FIFO** | The oldest element is always the first to leave |
| **Array struct fields** | `VALUE array[CAPACITY]` + `int front` + `int size` |
| **`front`** | Index of the oldest element — the next one to dequeue |
| **`size`** | Count of current elements in the queue |
| **Enqueue slot** | `array[(front + size) % CAPACITY]` — with wrap-around |
| **Dequeue** | Return `array[front]`, then `front++`, `size--` |
| **No deletion** | Dequeued data stays in the array — just `front` advances |
| **Queue full** | When `size == CAPACITY` |
| **Wrap-around** | `% CAPACITY` allows reuse of freed front slots |
| **Linked list struct** | DLL: `VALUE val` + `struct *prev` + `struct *next` |
| **Two pointers needed** | `head` (oldest, dequeue from here) + `tail` (newest, enqueue here) |
| **Enqueue (linked list)** | Append to tail: set prev/next, update tail's next, move tail |
| **Dequeue (linked list)** | Remove head: trav to next, free head, set trav->prev=NULL, head=trav |

### Enqueue and dequeue at a glance

```
Array-based:
  Enqueue: array[(front + size) % CAPACITY] = data;  size++;
  Dequeue: val = array[front];  front++;  size--;  return val;

Linked list-based:
  Enqueue: new->prev = tail;  new->next = NULL;  tail->next = new;  tail = new;
  Dequeue: trav = head->next;  free(head);  trav->prev = NULL;  head = trav;
```

### The FIFO pattern visualised

```
Enqueue sequence:  enqueue(28) → enqueue(33) → enqueue(19)

FRONT                                      BACK
  │                                          │
  ▼                                          ▼
[28] → [33] → [19]
  ↑               ↑
First in,      Last in,
first out      last out

Dequeue sequence:  dequeue() → 28,  dequeue() → 33,  dequeue() → 19
```
