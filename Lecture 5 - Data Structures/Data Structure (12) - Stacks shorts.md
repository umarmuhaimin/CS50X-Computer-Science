📦 Stacks — Data Structure (Lecture 5 — Data Structures @ CS50 | Doug Lloyd)

A stack is one of the most fundamental abstract data structures in computer science. It enforces a strict rule about how data enters and leaves — and that constraint, far from being a limitation, is exactly what makes it useful. This short covers what a stack is, how to implement it two different ways, and when you'd actually want to use one.

> **Important disambiguation:** The word "stack" has two meanings in CS50. The **stack segment of memory** (where local variables and function frames live) is different from the **stack data structure** covered here. They're named the same because they share the same LIFO principle — but they are separate concepts. This note is about the **data structure**.

---

## 1. 🧠 What Is a Stack?

A stack is a special kind of data structure that maintains data in an organised way, with one strict rule:

> **Only the most recently added element can be removed.**

Think of it like a real-world stack of plates. You add a plate on top, and when you need a plate, you take from the top. You can't pull one from the middle — the entire pile above it would collapse.

```
Stack of plates (real world analogy):

   [Plate 5]  ← added last, removed first (LIFO!)
   [Plate 4]
   [Plate 3]
   [Plate 2]
   [Plate 1]  ← added first, but can't be removed until all others are gone
```

Because of this behaviour, stacks are called **LIFO** structures:

> **LIFO — Last In, First Out**

The last element pushed onto the stack is the first element that will be popped off.

### Why would you want this?

When you want the most recently added data to be processed first. Real-world examples:
- **Browser back button** — the last page you visited is the first one you go back to
- **Undo in a text editor** — the most recent action is undone first
- **Function call stack** — the most recently called function is the first to return
- **Reversing a sequence** — push everything, then pop everything out in reverse

---

## 2. 📋 The Two Operations — Push and Pop

There are only **two legal operations** on a stack. No searching, no inserting in the middle, no accessing by index:

| Operation | What it does |
|-----------|-------------|
| **Push** | Add a new element to the **top** of the stack |
| **Pop** | Remove and return the **most recently added** element from the top |

That's it. The power of a stack comes from this simplicity and the discipline of the LIFO rule.

---

## 3. 🔧 Array-Based Implementation — The Struct

A stack can be built on top of an array. Here's the struct definition:

```c
typedef struct _stack
{
    VALUE array[CAPACITY];   // the array holding the data
    int top;                 // index of the next available slot (where the next push goes)
}
stack;
```

Breaking this down:

| Field | Type | Purpose |
|-------|------|---------|
| `array[CAPACITY]` | `VALUE[]` | The actual data storage — an array of fixed size |
| `top` | `int` | Tracks where the next element should go (the "top" of the stack) |

- **Clarification — `CAPACITY`:** This is a `#define` constant set elsewhere in your code. It caps the maximum number of elements the stack can hold. This is the key limitation of array-based stacks — the size is fixed at compile time.
- **Clarification — `VALUE`:** A placeholder for any data type (`int`, `char`, etc.). In Doug's examples, this is typically `int`.

### What the stack looks like in memory

When you declare `stack s;`, you get a block of memory with two parts:

```
stack s:
┌────┬────┬────┬────┬────┬────┬────┬────┬────┬────┐
│    │    │    │    │    │    │    │    │    │    │   ← array[CAPACITY] (10 slots)
├────┴────┴────┴────┴────┴────┴────┴────┴────┴────┘
│    │                                                 ← top (int, one slot)
└────┘

s
```

The top row (blue) is the array. The bottom-left green box is the `top` integer.

### Initialising the stack

```c
stack s;
s.top = 0;
```

Setting `s.top = 0` means: *the next push will go into `array[0]`*. The stack is empty.

```
After s.top = 0:
┌────┬────┬────┬────┬────┬────┬────┬────┬────┬────┐
│    │    │    │    │    │    │    │    │    │    │   ← all empty
├────┴────┴────┴────┴────┴────┴────┴────┴────┴────┘
│ 0  │                                                 ← top = 0
└────┘
s
```

---

## 4. ➕ Push — Array-Based

```c
void push(stack* s, VALUE data);
```

### Why does push take a **pointer** to the stack?

If you passed `stack s` directly (by value), C would make a copy of the entire stack struct and pass it to the function. Any changes inside `push` would affect the copy, not the original. Using `stack* s` (a pointer) ensures we're modifying the actual stack — not a temporary copy.

### What push does

```
a. Place the data at array[s->top]    (write the value into the next available slot)
b. Increment s->top                   (move the top marker up by one)
```

### Step-by-step walkthrough — push(28), push(33), push(19)

**Start:** `stack s; s.top = 0;`
```
array: [    |    |    |    |    |    |    |    |    |    ]
         0    1    2    3    4    5    6    7    8    9
top = 0
```

---

**`push(&s, 28);`** — place 28 at `array[0]`, then increment top to 1

```
Step 1: s.array[0] = 28   →  array: [ 28 |    |    | ... ]
Step 2: s.top = 1

array: [ 28 |    |    |    |    |    |    |    |    |    ]
          0    1    2    ...
top = 1   ↑
          next push goes here
```

---

**`push(&s, 33);`** — place 33 at `array[1]`, then increment top to 2

```
array: [ 28 | 33 |    |    |    |    |    |    |    |    ]
          0    1    2    ...
top = 2        ↑
               next push goes here
```

---

**`push(&s, 19);`** — place 19 at `array[2]`, then increment top to 3

```
array: [ 28 | 33 | 19 |    |    |    |    |    |    |    ]
          0    1    2    3    ...
top = 3             ↑
                    next push goes here
```

The stack now conceptually looks like:

```
          [19]  ← top (most recently added, removed first)
          [33]
          [28]  ← bottom (added first, removed last)
```

Even though it's stored horizontally in an array, logically you should think of it vertically — like a stack of plates.

---

## 5. ➖ Pop — Array-Based

```c
VALUE pop(stack* s);
```

### What pop does

```
a. Decrement s->top                   (logically remove the top element)
b. Return s->array[s->top]            (return the value that was just "removed")
```

> **Important:** Pop does NOT actually erase the data from the array. The value physically remains in memory, but by decrementing `top`, we're telling the stack to ignore it. If a future push happens, it will simply overwrite that slot. This is an efficient design — no need to zero out memory.

### Step-by-step walkthrough — pop sequence

**Starting state:** `array = [28, 33, 19, ...]`, `top = 3`

---

**`int x = pop(&s);`** — pop 19

```
Step 1: s.top-- → top becomes 2
Step 2: return s.array[2] → returns 19

x = 19

array: [ 28 | 33 | 19 |    |    | ... ]   ← 19 still in memory, but IGNORED
          0    1    2    3                    (top is now 2, so array[2] is "above" top)
top = 2        ↑
               top is now here (33 is the new top)
```

---

**`int x = pop(&s);`** — pop 33

```
Step 1: s.top-- → top becomes 1
Step 2: return s.array[1] → returns 33

x = 33

array: [ 28 | 33 | 19 |    |    | ... ]   ← 33 and 19 still in memory, IGNORED
          0    1    2    3
top = 1   ↑
          top is now here (28 is the new top)
```

---

**`push(&s, 40);`** — now push 40, which overwrites slot 1

```
Step 1: s.array[1] = 40   (overwrites the old 33)
Step 2: s.top = 2

array: [ 28 | 40 | 19 |    |    | ... ]   ← 33 gone, 40 in its place; 19 still ignored
          0    1    2    3
top = 2        ↑
               new top points here (40 is the current top)
```

This demonstrates the "pretend it isn't there" approach: when you pop, you don't erase — you just move the `top` marker down. When you push again, the new data clobbers whatever was left behind.

---

## 6. 🔗 Linked List-Based Implementation — The Struct

A stack can also be implemented using a linked list. The struct definition looks almost identical to a singly-linked list node:

```c
typedef struct _stack
{
    VALUE val;              // the data
    struct _stack *next;    // pointer to the next node
}
stack;
```

This is **exactly** a singly-linked list. The only difference is **programmer discipline**: we agree to only ever insert and delete at the **front** (head) of the list. We never insert in the middle, never delete from the middle. Just push at the front, pop from the front.

The front of the linked list is the **top of the stack**.

### The fundamental rule ⚠️

> **Always maintain a pointer to the head of the linked list.**

Without this, you lose access to the entire stack. In many implementations, this head pointer is declared as a **global variable** so it's always accessible.

---

## 7. ➕ Push — Linked List-Based

To push onto a linked list stack, you **prepend** a new node to the front — exactly like inserting at the front of a singly-linked list.

### Steps

```
a. Dynamically allocate (malloc) a new node
b. Check that malloc didn't return NULL
c. Set new->val to the data
d. Set new->next = list (connect new node to the old head)
e. Set list = new (new node becomes the new head)
```

### Visual walkthrough — `list = push(list, 12)` on `[15 → 9 → 13 → 10]`

**Starting state:**
```
list ──► [15|→] ──► [9|→] ──► [13|→] ──► [10|NULL]
```

**Step a+b — malloc new node:**
```
new ──► [  ?  |  ?  ]   ← garbage values
```

**Step c — set val = 12:**
```
new ──► [ 12  |  ?  ]
```

**Step d — `new->next = list` (connect to old head):**
```
new ──► [ 12  | ──►] ──► [15|→] ──► [9|→] ──► [13|→] ──► [10|NULL]
                list still points to 15 (unchanged so far)
```

**Step e — `list = new` (move head to new node):**
```
list ──► [ 12 | ──►] ──► [15|→] ──► [9|→] ──► [13|→] ──► [10|NULL]
```

12 is now the top of the stack. ✅

---

## 8. ➖ Pop — Linked List-Based

To pop from a linked list stack, you **remove the head node** — being careful not to lose the rest of the list.

### Steps

```
a. Create a traversal pointer (trav) pointing to the second element (head->next)
b. Free the current head
c. Move list to point to trav (the former second element becomes the new head)
d. Return the value that was stored in the old head
```

### Visual walkthrough — `pop(list)` on `[12 → 15 → 9 → 13 → 10]`

**Starting state:**
```
list ──► [12|→] ──► [15|→] ──► [9|→] ──► [13|→] ──► [10|NULL]
```

**Step a — create `trav` pointing to the second element:**
```
list ──► [12|→] ──► [15|→] ──► [9|→] ──► [13|→] ──► [10|NULL]
                     ↑
                    trav    (trav = list->next = &node_15)
```

Now we have two handles: `list` at node 12, `trav` at node 15. We can safely free 12 without losing the rest.

**Step b — `free(list)` — node 12 is gone:**
```
[FREED]             [15|→] ──► [9|→] ──► [13|→] ──► [10|NULL]
                     ↑
                    trav    (still safely pointing to 15)
```

**Step c — `list = trav` — move head to 15:**
```
list ──────────────► [15|→] ──► [9|→] ──► [13|→] ──► [10|NULL]
```

The stack is back to `[15 → 9 → 13 → 10]` — exactly where we were before pushing 12. ✅

**Step d — return the value (12)** to whatever called pop.

---

## 9. ⚖️ Array vs. Linked List Implementation

| Aspect | Array-Based Stack | Linked List-Based Stack |
|--------|------------------|------------------------|
| **Max size** | Fixed at `CAPACITY` | Unlimited (grows dynamically) |
| **Memory** | Pre-allocated, even if stack is small | Allocated per node as needed |
| **Push** | `array[top] = val; top++;` — O(1) | malloc + 2 pointer updates — O(1) |
| **Pop** | `top--;` — O(1) | trav + free + update head — O(1) |
| **Memory wasted** | Unused slots in array are wasted | Only allocated what's needed |
| **Overflow risk** | ✅ Yes — push onto full stack is a bug | ❌ No — limited only by system RAM |
| **Code complexity** | Simpler | Slightly more complex (malloc, null check) |
| **When to choose** | Known maximum size, simpler code | Unknown/variable size, no fixed cap |

Both implementations give O(1) push and pop — constant time regardless of stack size. The trade-off is memory flexibility vs simplicity.

---

## 10. 📌 Summary

| Concept | Detail |
|---------|--------|
| **Stack** | LIFO data structure — Last In, First Out |
| **Two operations only** | Push (add to top) and Pop (remove from top) |
| **LIFO** | The most recently added element is always the first removed |
| **Array implementation** | `VALUE array[CAPACITY]` + `int top` in a struct |
| **`top` field** | Index of the *next* available slot — always points one past the current top element |
| **Push (array)** | `array[top] = val; top++;` |
| **Pop (array)** | `top--;` then return `array[top]` — data stays but is "ignored" |
| **No actual deletion** | Popped data remains in the array; overwritten by the next push |
| **Linked list implementation** | Same as SLL — just restrict push/pop to the front only |
| **Push (linked list)** | Prepend: `new->next = list; list = new;` |
| **Pop (linked list)** | `trav = list->next; free(list); list = trav;` |
| **Why pointer param?** | Pass `stack*` not `stack` — to modify the actual struct, not a copy |
| **Array overflow** | Fixed capacity — push onto full array is a bug to handle |
| **Linked list advantage** | No fixed cap — grows as needed |
| **Linked list rule** | Always maintain the head pointer (often a global variable) |

### Push and pop at a glance

```
Array-based:
  Push: s.array[s.top] = val;  s.top++;
  Pop:  s.top--;  return s.array[s.top];

Linked list-based:
  Push: new->next = list;  list = new;
  Pop:  trav = list->next;  free(list);  list = trav;
```

### The LIFO pattern visualised

```
Push sequence:  push(28) → push(33) → push(19)

         [19]  ← added last, popped first
         [33]
         [28]  ← added first, popped last

Pop sequence:  pop() → 19,  pop() → 33,  pop() → 28
```
