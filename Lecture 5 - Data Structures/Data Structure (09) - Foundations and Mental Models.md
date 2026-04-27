🧠 Foundations and Mental Models (Lecture 5 — Data Structures @ CS50)

This file captures the essential conceptual clarity behind data structures in C. These are not syntax rules — they are the *mental models* that make everything else make sense. Every concept here resolves a real point of confusion and builds toward a complete, accurate picture of how data structures actually work in memory.

---

## 1. 🏗️ The Hierarchy — Data Types, Custom Types, and Data Structures

When people say "data structures," the term is broad enough to cause confusion. Here's the full picture laid out clearly:

| Level | Examples | What it is |
|-------|----------|------------|
| **Built-in data types** | `int`, `char`, `double`, `bool`, `string` | A single value of a single type — the raw building blocks given by C |
| **Custom data types** | `struct car`, `struct node` | A bundled group of fields you define yourself using `struct` |
| **Data structures** | Array, Linked List, Hash Table, Tree, Trie, Stack, Queue | An organised *system* for storing, connecting, and accessing many values |

The simplest analogy:

```
Data type      =  a single brick
Data structure =  a building made of bricks, arranged with purpose
```

**Arrays** sit in between — technically the simplest data structure (a contiguous block of the same type), but so fundamental to C that we treat them as almost built-in.

The key distinction between a data type and a data structure: **a data structure isn't just about what you store — it's about how the data is connected and accessed.**

- A linked list isn't just "nodes" — it's the *chain of pointers* between them that makes it a structure.
- A hash table isn't just an array — it's the *hash function + buckets + chains* working together.
- A binary search tree isn't just nodes with two pointers — it's the *BST property + recursive search logic* that defines it.

---

## 2. 🔵 Custom Data Type vs Data Structure — The Precise Distinction

This is the most important conceptual separation to understand.

### Custom data type = `struct` definition only (the blueprint)

```c
typedef struct node
{
    int number;
    struct node *next;
}
node;
```

This alone is just a **blueprint**. Nothing exists in memory yet. No list, no connections, nothing. The compiler now knows what a `node` looks like and how big it is — but no actual node has been created anywhere.

### Data structure = type + the full system

```c
node *list = NULL;         // starting to BUILD the data structure
node *n = malloc(...);     // allocating actual nodes in memory
n->next = list;            // connecting them with pointers
list = n;                  // maintaining the head
// + search logic
// + free logic
```

All of this together — the struct definition, the memory allocation, the pointer connections, the insertion and search logic, the memory management — is the **linked list data structure**.

### Side by side

```
Custom data type  =  struct definition only
                     (just the blueprint — no memory, no logic)

Data structure    =  struct/existing types  +  the full system
                     (blueprint + connections + logic + memory management)
```

### Real examples

| What you write | What it is |
|----------------|-----------|
| `struct node { int number; struct node *next; };` | Custom data type |
| `struct node` + malloc + prepend + search + free | Linked list **data structure** |
| `int` (already exists) + array + binary search logic | Binary search **data structure** |
| `struct node` + left/right pointers + recursive search | Binary search tree **data structure** |

Notice the last two — **you don't even need a custom type to build a data structure.** An array of plain `int`s with the right logic around it is still a data structure. The system is what makes it a data structure, not the type.

---

## 3. 🔗 The Full Chain of Abstraction

Every data structure you will ever build follows this same three-level chain:

```
int, char, double          →  built-in data types (given by C)
         ↓
struct node { ... }        →  custom data type (you define the blueprint)
         ↓
nodes + pointers + logic   →  linked list data structure (you build the system)
```

You go up levels of abstraction each time — from raw types, to a blueprint, to a fully working system. **Define the node type first, then build the system on top of it.** This pattern repeats for every data structure you'll ever build.

### Answering the common question

> *"When someone says they created their own data structure — is that correct?"*

It depends on what they built:

- If they only wrote a `struct` definition → they created a **custom data type**, not a data structure yet.
- If they wrote the struct *plus* all the logic around it → they created a **data structure**. Saying "I built my own data structure" is completely valid and standard industry language.

The precise statement would be: *"I defined a custom data type (`struct`) and used it to implement a data structure."* But in day-to-day conversation, nobody talks like that. The distinction only truly matters in exams or technical interviews.

---

## 4. 📌 The Head — Everything You Need to Know

The head is one of the most important concepts in linked lists, and also one of the most misunderstood.

### What is the head?

The head is **just a pointer** — a single variable that stores the address of the **first node** in the list. That's it. Nothing special about the memory, nothing special about the node it points to. Its entire importance comes from one fact:

> **It is your only way into the list. Lose it, and you lose everything — permanently.**

```c
node *list = NULL;
      ↑
      This IS the head.
      A single pointer variable that holds the address of the first node.
      NULL means the list is empty.
```

### The head is NOT a node ⚠️

This is the most common misconception. The head is not part of the list — it is the **handle** you hold to access the list.

```
list (head)          Node 1           Node 2           Node 3
┌─────────┐        ┌──────────┐     ┌──────────┐     ┌──────────┐
│  0x100  │───────►│ 3 │ 0x200│────►│ 2 │ 0x300│────►│ 1 │ NULL │
└─────────┘        └──────────┘     └──────────┘     └──────────┘
  (stack)                        (all on the heap)
```

The head lives on the **stack**. The nodes live on the **heap**. The head simply knows where the first node is.

### The one-line definition

> **Head = a pointer variable that always holds the address of the first node in the list.**

Three key properties that flow from this:

```
1. Lives on the stack        →  not a node, just a variable holding an address
2. Always points to first    →  entry point to the entire list
3. NULL means empty list     →  no first node = no list
```

### What you MUST know about the head

**1. It starts as NULL — representing an empty list**
```c
node *list = NULL;   // no nodes yet — list is empty
```

**2. It always points to the FIRST node — not the last, not the middle**
```c
// After inserting 3 nodes by prepending (typing 1, 2, 3):
list ──► [ 3 ] ──► [ 2 ] ──► [ 1 ] ──► NULL
          ↑
          head always points HERE (most recently prepended node)
```

**3. When you prepend, the head MUST be updated**
```c
n->next = list;   // new node points to the old first node
list = n;         // head now points to the new node
                  // forgetting this second line orphans the new node
```

**4. NEVER overwrite the head without saving it first**
```c
list = malloc(...);   // ❌ DISASTER
                      // The entire list behind the old head is now
                      // lost forever — a permanent memory leak
```

**5. Every operation starts from the head**
```
Traversal  →  start at list, follow ->next until NULL
Freeing    →  start at list, free each node one by one
Searching  →  start at list, compare each node's value
```

**6. If the head is NULL, the list is empty**
```c
if (list == NULL)
{
    // no nodes exist — list is empty
}
```

**7. The head can be a local variable, global variable, or parameter**
```c
node *list = NULL;        // local variable (inside a function)
node *list = NULL;        // global variable (outside all functions)
void search(node *list)   // parameter (passed into a function)
```

All three are just a pointer variable holding an address. The scope differs, but the behaviour is identical — they all point to the first node the same way.

### The golden rule

```
The head is the list.
If you have the head, you have access to everything.
If you lose the head, you lose everything — permanently.
```

This is why every linked list function either takes the head as a parameter or returns a new head. It is the single thread connecting you to all nodes in memory.

---

## 5. 🔴 NULL — Two Meanings, One Symbol

`NULL` appears in two different places in a linked list. They look the same but mean completely different things depending on *where* you see it.

```
head = NULL          →  no nodes exist, list is empty

node->next = NULL    →  this is the last node, end of the list
```

### Side by side

| Where you see NULL | What it means |
|-------------------|---------------|
| `head == NULL` | The **list itself is empty** — no nodes exist at all |
| `ptr->next == NULL` | You're at the **last node** — end of the list, stop here |

### The complete navigation logic of a linked list

```
head = NULL              →  empty list, nothing to traverse
head = address           →  list exists, start here
node->next = address     →  follow the arrow to the next node
node->next = NULL        →  end of the list, stop here
```

That's the entire navigation logic in four lines. Every traversal, search, and free loop in a linked list is built on exactly these four states.

---

## 6. 🔧 `malloc` and `sizeof` — The Dependency Chain

You can only call `malloc(sizeof(node))` **after** the `node` struct has been defined. This is not optional — if the compiler doesn't know what `node` is, it can't calculate its size.

### The required order

```
Step 1 — Define the blueprint (struct):
typedef struct node
{
    int number;        →  4 bytes
    struct node *next; →  8 bytes
}
node;
↑ Now the compiler knows what a "node" is and exactly how big it is.

Step 2 — Only NOW can you malloc one:
node *n = malloc(sizeof(node));
```

If you tried `malloc(sizeof(node))` without defining the struct first, the compiler would say: *"what is a node? I don't know that type"* and refuse to compile.

### Why `sizeof(node)` and not a hardcoded number?

`malloc` needs to know **how many bytes** to allocate. `sizeof(node)` asks the compiler to calculate the exact size automatically:

```
struct node
{
    int number;        →  4 bytes
    struct node *next; →  8 bytes (pointer — always 8 bytes on 64-bit)
}
                          ────────
                          12 bytes total
```

So `malloc(sizeof(node))` is equivalent to `malloc(12)` — but you should always use `sizeof` because:

```
malloc(12)             ❌ fragile — breaks if you ever change the struct
malloc(sizeof(node))   ✅ always correct — automatically adjusts to any changes
```

If you later added a `char name[50]` field to the struct, `sizeof(node)` would automatically reflect the new size. `malloc(12)` would silently allocate too little memory and corrupt things.

### The full sequence — nothing can be skipped or reordered

```
1. Define struct     →  compiler learns what "node" is
2. sizeof(node)      →  compiler calculates exact byte size of one node
3. malloc(...)       →  OS allocates that many bytes on the heap
4. node *n = ...     →  n points to that freshly allocated memory
```

---

## 7. 📏 Pointer Size — Always 8 Bytes on Modern Systems

Every pointer — regardless of what type it points to — is the same size on the same system.

```
int *p         →  8 bytes
char *p        →  8 bytes
double *p      →  8 bytes
node *p        →  8 bytes
struct car *p  →  8 bytes
```

### Why always 8 bytes?

A pointer stores a **memory address**, not the actual data. On a 64-bit system, every memory address is a 64-bit number — that's 8 bytes. The type (`int`, `char`, `node`) only tells the compiler *how to interpret* the data at that address, not how big the pointer itself is.

### The street address analogy

```
"42 Wallaby Way, Sydney"   →  always the same length address
                               regardless of whether the house is
                               a tiny flat or a massive mansion
```

The address (pointer) is always the same size. The house (the data it points to) can be any size.

```
int *p     →  [ 0x100 ] (8 bytes) ───► [ 42 ] (4 bytes — the int)
node *p    →  [ 0x200 ] (8 bytes) ───► [ number | next ] (12 bytes — the node)
```

The pointer is always 8 bytes. What it *points to* varies.

### On different systems

| System | Pointer size |
|--------|-------------|
| 64-bit (modern Mac, PC, CS50 codespace) | **8 bytes** |
| 32-bit (older systems) | 4 bytes |

This is why `sizeof(node *)` and `sizeof(int *)` both return 8 on your machine — same system, same address size, always.

---

## 8. 📌 Summary — The Complete Mental Model

### The hierarchy

```
int, char, double, bool    →  built-in data types (given by C)
struct node { ... }        →  custom data type (you define the blueprint)
nodes + pointers + logic   →  data structure (you build the full system)
```

### Custom type vs data structure

```
struct definition only     =  custom data type (blueprint, no memory yet)
struct + full system       =  data structure (connections + logic + memory)
```

### The head

```
head = pointer variable holding the address of the first node
head = NULL     →  list is empty
head != NULL    →  list exists, start traversal here
Lose the head   →  lose the entire list permanently
```

### NULL — two meanings

```
head == NULL         →  list is empty (no nodes)
node->next == NULL   →  last node (end of list)
```

### malloc + sizeof

```
Define struct first  →  compiler knows the type
sizeof(node)         →  compiler calculates exact byte size
malloc(sizeof(node)) →  OS allocates that many bytes on the heap
```

### Pointer size

```
All pointers on 64-bit systems  →  always 8 bytes
The type (*int, *node, *char)   →  only affects how to interpret the data,
                                   not the size of the pointer itself
```

| Concept | Key fact |
|---------|----------|
| **Data type** | Single value, single type — `int`, `char`, `double` |
| **Custom data type** | `struct` definition — a blueprint for a new type |
| **Data structure** | Any type + full system of connections, logic, memory management |
| **Head** | Pointer variable holding the address of the first node |
| **`head == NULL`** | Empty list — no nodes exist |
| **`node->next == NULL`** | Last node — end of the list |
| **`sizeof(node)`** | Exact byte size of one node — calculated automatically by the compiler |
| **`malloc(sizeof(node))`** | Requires struct to be defined first |
| **Pointer size** | Always 8 bytes on 64-bit systems, regardless of the pointed-to type |
