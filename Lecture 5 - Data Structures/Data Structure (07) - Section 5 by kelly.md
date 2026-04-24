🎓 Data Structures — Section 5 by Kelly (Lecture 5 @ CS50)

This section by Kelly (CS50 Preceptor) bridges the gap between the lecture and Problem Set 5 (Speller). The focus is on building practical intuition: how to *decide* which data structure to use, how to *build* a linked list from scratch in code, and how to *write and improve* your own hash function.

---

## 1. 🧠 Choosing a Data Structure — Start With the Operations

Before picking any data structure, ask yourself: **what will I actually be doing with this data?**

Kelly opens with a real scenario: you work for a company building a voice assistant. The assistant currently only wakes up when it hears "hello." Your team wants it to also respond to "hi", "low", "hey", and "hello there." You need to store this list of wake words.

**The question:** what data structure should you use?

The answer depends entirely on **which operations you'll use most**, and **how fast each needs to be**:

| Operation | Meaning |
|-----------|---------|
| **Search** | Check if a given word is in the list |
| **Insert** | Add a new wake word |
| **Delete** | Remove a word that's no longer needed |

You also need to **prioritise**. If users will be searching for words constantly but inserting rarely, optimise for search speed. If you're bulk-loading thousands of words at startup, optimise for insertion.

> **Key insight:** There is no universally "best" data structure. The right choice depends entirely on the context — what operations you need, how often, and what you're willing to trade off.

---

## 2. 🗺️ Three Data Structures at a Glance

Kelly shows three possible structures for storing the wake words. Here's what each looks like visually:

### Option A — Linked List

A single chain of nodes. Each node holds a word and a pointer to the next node.

```
list (head)
  │
  ▼
┌──────────┬──────┐     ┌──────────┬──────┐     ┌───────────┬──────┐
│ "hello"  │  ●───┼────►│  "hey"   │  ●───┼────►│  "low"    │ NULL │
└──────────┴──────┘     └──────────┴──────┘     └───────────┴──────┘
```

Simple. Dynamic. But searching means scanning every node one by one — **O(n)**.

---

### Option B — Hash Table

An array of buckets. Each bucket is its own linked list. Words are placed into buckets based on a rule (e.g. first letter).

```
Buckets (by first letter):
 [0] A │ NULL
 [1] B │ NULL
 ...
 [7] H │ ──► ["hello"|●] ──► ["hey"|●] ──► ["hi"|NULL]
 ...
[11] L │ ──► ["low"|NULL]
 ...
[19] T │ ──► ["there"|NULL]
 ...
[25] Z │ NULL
```

Faster search: if you're looking for "hey", jump straight to bucket H — no need to check L, T, or any other bucket. But uses much more memory (26 buckets, most empty).

---

### Option C — Trie

A tree where each node is an array of 26 pointers (one per letter). Words are stored implicitly as paths through the tree.

```
Root
 └──[H]──► Node
             ├──[E]──► Node
             │          ├──[L]──► Node
             │          │          └──[L]──► Node
             │          │                    └──[O]──► [is_word: TRUE] ✅ "hello"
             │          ├──[Y]──► [is_word: TRUE] ✅ "hey"
             │          └──[I]──► [is_word: TRUE] ✅ "hi"
 └──[L]──► Node
             └──[O]──► Node
                        └──[W]──► [is_word: TRUE] ✅ "low"
```

Search is always O(1) — only as many steps as the word is long. But memory cost is very high (26 pointers per node, most of them NULL).

---

## 3. ⚖️ The Core Trade-Off — Time vs. Space

Kelly references the **CS50 Speller Big Board** — a leaderboard of student submissions for Problem Set 5. The observation:

```
╔════════════════════════════════════════════╗
║  Fastest implementation  ←→  Most memory  ║
╠════════════════════════════════════════════╣
║  Rank 1   │  ~0.05s  │  ~80 MB used        ║
║  Rank 5   │  ~0.10s  │  ~20 MB used        ║
║  Rank 10  │  ~0.20s  │  ~8 MB used         ║
╚════════════════════════════════════════════╝
```

The fastest solution used the most memory. The most memory-efficient solution was slower. This is the fundamental trade-off in data structures — **you can't have both for free**.

| Want | Pay |
|------|-----|
| Faster search | More memory (more buckets, more pointers) |
| Less memory | Slower search (longer chains, fewer buckets) |

---

## 4. 🔗 Linked Lists — What Is a Node?

To build a linked list that stores words, each **node** needs two things:

```c
typedef struct node
{
    char *phrase;        // the word/phrase we're storing
    struct node *next;   // address of the next node in the list
}
node;
```

For the numbers version (used in `list.c`):

```c
typedef struct node
{
    int number;          // the integer we're storing
    struct node *next;   // address of the next node
}
node;
```

- **Clarification — `struct node *next` inside the struct:** The compiler reads code top to bottom. When it sees `struct node *next` inside the struct definition, it needs to already know what `struct node` is. That's why you write `struct node` on the opening line — it gives the struct a name before the definition is complete. Without this, the compiler would refuse to compile.

---

## 5. 🏗️ Creating a Linked List — Step by Step

### Starting empty

```c
node *list = NULL;   // list is a pointer to the head; starts as empty (NULL)
```

```
list ──► NULL    (empty list)
```

### Allocating a new node

```c
node *n = malloc(sizeof(node));   // ask OS for enough memory for one node
if (n == NULL)
{
    printf("malloc failed\n");
    return 1;
}
```

Always check for NULL. If malloc fails (not enough memory), it returns NULL. Using a NULL pointer without checking will crash your program.

```
n ──► [ number:? | next:? ]    (garbage values — not initialised yet)
```

### Filling in the node

```c
n->number = x;    // store the user's number
n->next = NULL;   // no next node yet
```

```
n ──► [ number:x | next:NULL ]
```

### Prepending to the list (inserting at the front)

```c
n->next = list;   // new node points to whatever was the old head
list = n;         // update head to be the new node
```

**Visual — inserting 1, then 2, then 3:**

```
After inserting 1:
list ──► [ 1 | NULL ]

After inserting 2:
list ──► [ 2 | ──► ] ──► [ 1 | NULL ]

After inserting 3:
list ──► [ 3 | ──► ] ──► [ 2 | ──► ] ──► [ 1 | NULL ]
```

> **Why does the order reverse?** Every new node is inserted at the *front* — it becomes the new head. So the last number entered (3) ends up first in the list. This is LIFO behaviour (same principle as a stack). When you print from the head, you get 3, 2, 1 — not 1, 2, 3.

---

## 6. 💻 list.c — Full Program Walkthrough

```c
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
    node *list = NULL;          // start with an empty list

    // Build list — get 3 numbers from user
    for (int i = 0; i < 3; i++)
    {
        int x = get_int("Number: ");

        node *n = malloc(sizeof(node));
        if (n == NULL)
        {
            printf("malloc failed\n");
            return 1;
        }

        n->number = x;
        n->next = list;         // prepend: point new node at current head
        list = n;               // new node becomes the new head
    }

    // Print all nodes
    for (node *ptr = list; ptr != NULL; ptr = ptr->next)
    {
        printf("%i\n", ptr->number);
    }

    // Free all nodes
    node *ptr = list;
    while (ptr != NULL)
    {
        node *temp = ptr;       // save current node
        ptr = ptr->next;        // move pointer forward BEFORE freeing
        free(temp);             // now safe to free the saved node
    }

    return 0;
}
```

**Running it with inputs 1, 2, 3 prints: 3, 2, 1** — because each new number was prepended, making it the new head.

- **Clarification — why use a for loop for printing but while loop for freeing?** Both work for traversal. The for loop is more compact. The while loop for freeing needs the `temp` variable trick — it's slightly clearer to write this out as a while loop so the save-then-free logic is on separate lines and easy to follow.

---

## 7. 🗑️ Freeing a Linked List — The Right Way

### The wrong way ❌

```c
free(list);    // only frees the first node!
```

```
Before:
list ──► [ 3 | ──► ] ──► [ 2 | ──► ] ──► [ 1 | NULL ]

After free(list):
list ──► [FREED]     ──► [ 2 | ──► ] ──► [ 1 | NULL ]
                              ↑
                     This node is now orphaned — pointer to it was inside
                     the freed node. You can never reach or free it.
                     This is a MEMORY LEAK. ❌
```

### The correct way ✅

You must free each node **one at a time**, but save the pointer to the *next* node **before** freeing the current one:

```c
node *ptr = list;
while (ptr != NULL)
{
    node *temp = ptr;       // step 1: save current node address
    ptr = ptr->next;        // step 2: advance ptr to next node
    free(temp);             // step 3: now free the saved (old) current node
}
```

**Visual walkthrough with list `[3] → [2] → [1]`:**

```
Iteration 1:
  temp = ptr  →  temp points to [3]
  ptr = ptr->next  →  ptr now points to [2]
  free(temp)  →  [3] is freed ✅

Iteration 2:
  temp = ptr  →  temp points to [2]
  ptr = ptr->next  →  ptr now points to [1]
  free(temp)  →  [2] is freed ✅

Iteration 3:
  temp = ptr  →  temp points to [1]
  ptr = ptr->next  →  ptr = NULL
  free(temp)  →  [1] is freed ✅

ptr is now NULL → while loop ends. All nodes freed, no leaks. ✅
```

> **Why not `free(ptr)` then `ptr = ptr->next`?** Once you `free(ptr)`, the memory at that address is returned to the OS. Accessing `ptr->next` after freeing `ptr` is **undefined behaviour** — you're reading memory you no longer own. The program may crash, produce garbage, or silently corrupt data.

---

## 8. 🔬 Deep Dive — Pointer Mechanics, Freeing, and Why the Output Is Reversed

This section captures a detailed Q&A that resolves the most common points of confusion when building and freeing a linked list in C. Every question here is one you will almost certainly ask yourself while working on this topic.

---

### 8.1 — Core Concept: `list->next` Is a READ, Not a MOVE

The most common misconception: people think `list->next` *moves* the `list` pointer. It doesn't.

> **`list->next` means:** go to the node that `list` currently points to, and **read** the value stored in its `next` field. `list` itself does not change position at all.

It is exactly equivalent to writing `(*list).next` — dereference `list`, then access the `next` field inside.

| What you might think | What actually happens |
|----------------------|-----------------------|
| `list->next` moves `list` to the next node | `list` stays exactly where it is — it is not touched |
| `ptr` stays on the current node's data | `ptr` gets the value stored in `next` — the address of the next node |

---

### 8.2 — Step-by-Step Freeing With Full Diagrams

Let's trace through freeing a two-node list (`"Alice" → "Bob"`) to make every step crystal clear.

**Starting state:**
```
list
 |
 v
[ "Alice" | next ]────►[ "Bob" | NULL ]
   Node A                  Node B
```

---

**Step 1: `node *ptr = list->next;`**

`list->next` reads the `next` field inside Node A — which holds the address of Node B.
`ptr` now stores that address. `list` is completely unchanged.

```
list          ptr
 |             |
 v             v
[ "Alice" | next ]────►[ "Bob" | NULL ]
   Node A                  Node B
```

---

**Step 2: `free(list);`**

Node A is deallocated **entirely** — both `"Alice"` AND its `next` field are gone.
`ptr` still safely points to Node B. Nothing about `ptr` changed.

```
list          ptr
 |             |
 v (freed)     v
[   GONE   ]  [ "Bob" | NULL ]
                 Node B
```

---

**Step 3: `list = ptr;`**

`list` is **reassigned** (or **advanced**) to point to Node B — the same node `ptr` points to.
Now we're ready to repeat the loop on Node B.

```
              list
               |
               v
             [ "Bob" | NULL ]
                Node B
```

---

**Step 4: Loop repeats — `ptr = list->next;`**

`list->next` is now `NULL` (Node B has no next node).
So `ptr = NULL`.

```
              list    ptr = NULL
               |
               v
             [ "Bob" | NULL ]
                Node B
```

Then `free(list)` removes Node B, and `list = ptr` sets `list = NULL`.
The loop condition (`list != NULL`) fails — loop ends. ✅

---

### 8.3 — Terminology: What Does `list = ptr` Actually Mean?

The word "replace" works but can be misleading. Here are more precise terms:

| Term | What it means |
|------|--------------|
| **"list is reassigned to ptr"** | Most accurate — you're changing what address `list` stores |
| **"list now points to what ptr points to"** | Emphasises both pointers end up at the same node |
| **"list advances to the next node"** | Most intuitive when walking a linked list in a loop |

In the loop context, **"advance"** is the most natural — you're moving `list` forward one step along the chain. "Replace" can be confusing because it might imply `ptr` disappears, but both variables still exist — `list` simply gets updated to hold the same address `ptr` holds.

---

### 8.4 — Stack vs Heap: Why You Never `free(ptr)`

A question that naturally follows: after `list = ptr`, what happens to `ptr`? Do we need to free it?

**No. You never free `ptr`.** Here's why:

`ptr` is not a node. It's a **local variable** — it lives on the **stack** and is destroyed automatically when the function ends. It has no memory of its own on the heap.

```
Stack                      Heap
───────────────────        ──────────────────────────
list  [ 0x456 ] ──┐
                  └──────► [ "Bob" | NULL ]   ← this lives on the heap
ptr   [ 0x456 ] ──┘            ↑
───────────────────       needs freeing
```

Both `list` and `ptr` are just variables holding the **same address**. The node itself is one thing in one place in memory. Two pointers happen to be pointing at it.

After `list = ptr`, `ptr` still holds the old address — the line `list = ptr` only changes `list`, it doesn't touch `ptr` at all. On the next loop iteration, `ptr` simply gets overwritten with the new `list->next` value and reused.

**The key mental model:** `free` is for **heap memory** (the nodes themselves). Pointer variables live on the stack — they clean themselves up automatically. You free the **thing being pointed at**, not the pointer itself.

---

### 8.5 — Why Does the Output Print 3, 2, 1 Instead of 1, 2, 3?

When you build the list by typing 1, then 2, then 3, the output is `3 2 1`. This surprises many people. Here's exactly why.

**The struct:**
```c
typedef struct node {
    int number;
    struct node *next;
} node;
```

Each node looks like: `[ number | next ]`

**Building the list — first loop (`n->next = list; list = n;`)**

You always **prepend** — every new node is inserted at the **front**. Watch what happens:

```
Start: list = NULL

After typing 1 (i=0):
  n->next = list  →  n->next = NULL
  list = n        →  list now points to node 1

  list ──► [ 1 | NULL ]

After typing 2 (i=1):
  n->next = list  →  n->next points to node 1
  list = n        →  list now points to node 2

  list ──► [ 2 | ──►] ──► [ 1 | NULL ]

After typing 3 (i=2):
  n->next = list  →  n->next points to node 2
  list = n        →  list now points to node 3

  list ──► [ 3 | ──►] ──► [ 2 | ──►] ──► [ 1 | NULL ]
```

**Each new number becomes the new front.** The last number typed (3) ends up at the head of the list. When you print starting from the head, you get **3, 2, 1**.

This is called **prepending**. It has the same LIFO property as a stack — last in, first out.

**Printing the list — second loop:**

```c
for (node *ptr = list; ptr != NULL; ptr = ptr->next)
```

| Step | `ptr` points to | Prints |
|------|-----------------|--------|
| Start | Node 3 | `3` |
| `ptr = ptr->next` | Node 2 | `2` |
| `ptr = ptr->next` | Node 1 | `1` |
| `ptr = ptr->next` | NULL | loop ends |

---

### 8.6 — The Free Loop, Line by Line

This is the most detailed breakdown of the freeing process. Each line of the loop is explained with a diagram.

**Starting state** (after building the list with 1, 2, 3):
```
list ──► [ 3 | •]──► [ 2 | •]──► [ 1 | NULL ]
```

**Setup: `node *ptr = list;`**

`ptr` is created and starts at the same place as `list` — the front of the list (Node 3).

```
list ──► [ 3 | •]──► [ 2 | •]──► [ 1 | NULL ]
ptr  ────────^
```

---

**Iteration 1:**

**Line 1 — `node *tmp = ptr;`**
`tmp` is created and points to the same node as `ptr` — Node 3. Nothing is freed yet.
```
list ──► [ 3 | •]──► [ 2 | •]──► [ 1 | NULL ]
ptr  ────────^
tmp  ────────^
```

**Line 2 — `ptr = ptr->next;`**
`ptr` **advances** to Node 2. `tmp` stays behind at Node 3.
This is the critical step — you've saved Node 3's address in `tmp` **before** moving forward.
```
list ──► [ 3 | •]──► [ 2 | •]──► [ 1 | NULL ]
tmp  ────────^         ^
ptr  ──────────────────^
```

**Line 3 — `free(tmp);`**
Node 3 is destroyed. `ptr` is safely already at Node 2 — nothing was lost.
```
list ──► [ GONE ]   [ 2 | •]──► [ 1 | NULL ]
                      ^
ptr  ─────────────────^
```

> `tmp` still technically holds the old address, but that memory is now freed. You must never use `tmp` again until it's reassigned.

---

**Iteration 2:**

```
node *tmp = ptr;      →  tmp points to Node 2

list ──► [ GONE ]   [ 2 | •]──► [ 1 | NULL ]
ptr  ─────────────────^
tmp  ─────────────────^

ptr = ptr->next;      →  ptr advances to Node 1

tmp  ──► [ 2 | •]──► [ 1 | NULL ]
                       ^
ptr  ──────────────────^

free(tmp);            →  Node 2 destroyed

[ GONE ]   [ 1 | NULL ]
            ^
ptr  ───────^
```

---

**Iteration 3:**

```
node *tmp = ptr;      →  tmp points to Node 1

ptr  ──► [ 1 | NULL ]
tmp  ────────^

ptr = ptr->next;      →  ptr->next is NULL, so ptr = NULL

tmp  ──► [ 1 | NULL ]
ptr  ──► NULL

free(tmp);            →  Node 1 destroyed

tmp  ──► [ GONE ]
ptr  ──► NULL
```

`ptr == NULL` → the `while` condition fails → loop ends. All 3 nodes freed. ✅

---

### 8.7 — Summary: The Golden Rule of Freeing a Linked List

| Line | What it does | Why |
|------|-------------|-----|
| `node *ptr = list;` | Start `ptr` at the front of the list | Need a way to walk the list |
| `node *tmp = ptr;` | Save the address of the current node | So we don't lose it after advancing `ptr` |
| `ptr = ptr->next;` | Advance `ptr` to the next node | Move forward **before** destroying the current node |
| `free(tmp);` | Destroy the current node | Safe — `ptr` is already ahead, nothing is lost |

> **The golden rule:** always save `next` before freeing the current node. Once `free` is called, the node — including its `next` field — is gone forever. If you didn't save `next` first, the chain is permanently broken and the remaining nodes leak.

---

## 9. #️⃣ Hash Tables — Faster Search via Bucketising

A linked list has O(n) search — you may have to scan every node. A **hash table** reduces this by splitting the data into smaller buckets, so you only search within one bucket.

```
Linked list search (O(n)):
list ──► ["hello"] ──► ["hey"] ──► ["hi"] ──► ["low"] ──► ["there"] ──► NULL
         checked!      checked!    checked!   ← if searching for "low", 4 checks

Hash table search (O(1) best case):
hash("low") = 11 (L bucket)
Bucket[11] ──► ["low"] ──► NULL
               found in 1 check! ✅
```

A hash table is an **array of linked lists**:

```c
node *table[26];    // 26 buckets, one per letter of the alphabet
                    // each element is a pointer to a linked list (initially NULL)
```

```
table[0]  (A) ──► NULL
table[1]  (B) ──► NULL
...
table[7]  (H) ──► ["hello"|●] ──► ["hey"|●] ──► ["hi"|NULL]
...
table[11] (L) ──► ["low"|NULL]
...
table[25] (Z) ──► NULL
```

The **hash function** decides which bucket a given word goes into. It takes a word as input and returns an index (0–25).

```
hash("hey")   → 7   (H is the 8th letter, 0-indexed = 7)
hash("low")   → 11  (L is the 12th letter, 0-indexed = 11)
hash("there") → 19  (T is the 20th letter, 0-indexed = 19)
```

---

## 10. 🔧 Coding a Hash Function — hash.c Walkthrough

### Version 1 — Map by first letter (basic)

```c
#include <ctype.h>
#include <stdio.h>
#include <string.h>

int hash(char *word);   // prototype — must be declared before main uses it

int main(void)
{
    char *word = get_string("Word: ");
    printf("Hash value: %i\n", hash(word));
}

int hash(char *word)
{
    // Edge case: empty string or NULL
    if (word == NULL || strlen(word) == 0)
    {
        return -2;
    }

    char c = toupper(word[0]);   // take first character, force uppercase

    // Edge case: non-alphabetical first character
    if (!isalpha(c))
    {
        return -1;
    }

    return c - 'A';   // A→0, B→1, C→2, ... Z→25
}
```

### Breaking down `c - 'A'`

| First letter | ASCII value | `- 'A'` (65) | Result |
|-------------|------------|--------------|--------|
| 'A' | 65 | 65 - 65 | 0 |
| 'B' | 66 | 66 - 65 | 1 |
| 'C' | 67 | 67 - 65 | 2 |
| 'Z' | 90 | 90 - 65 | 25 |

- **Clarification — `toupper` before subtracting:** A lowercase 'a' has ASCII value 97, not 65. If you subtract 'A' (65) from 'a' (97), you get 32 — which is wrong. By forcing the character to uppercase first, you guarantee the arithmetic works correctly regardless of the input's case.

### Edge cases handled

| Input | Returns | Why |
|-------|---------|-----|
| `NULL` | -2 | Pointer is null — indexing into it would crash |
| `""` (empty string) | -2 | `strlen == 0` — no first character to inspect |
| `"123abc"` | -1 | First character is not alphabetical |
| `"cat"` | 2 | 'C' - 'A' = 67 - 65 = 2 ✅ |
| `"CAT"` | 2 | `toupper('C')` = 'C' → same result ✅ |

---

## 11. 🔬 Deep Dive — Hash Function Code and Improvements (Q&A)

This section captures a detailed Q&A that explains the hash.c code from scratch — what every line does, why it works, and how to make it better.

---

### 11.1 — What Is a Hash Function? (The Filing Cabinet Analogy)

A hash function takes a word and converts it into a **number**. That number is used as a **bucket index** to know where to store or find the word.

Think of it like alphabetical filing cabinets:

```
Cabinet  0  →  words starting with A  (apple, ant, arrow...)
Cabinet  1  →  words starting with B  (ball, bear, book...)
Cabinet  2  →  words starting with C  (cat, car, cake...)
...
Cabinet 25  →  words starting with Z  (zoo, zero, zoom...)
```

The hash function's job is: **given a word, which cabinet number does it belong in?**

---

### 11.2 — Kelly's hash.c — Complete Code

From the section exercise, here is the complete hash.c program:

```c
#include <cs50.h>
#include <stdio.h>
#include <ctype.h>
#include <string.h>

int hash(char *word);   // prototype — must appear before main uses it

int main(void)
{
    char *word = get_string("Word: ");
    printf("Hash value: %i\n", hash(word));
}

int hash(char *word)
{
    if (word == NULL || strlen(word) == 0)
    {
        return -1;
    }
    char c = word[0];
    if (isalpha(c))
    {
        c = toupper(c);
        return c - 'A';
    }
    return -1;
}
```

---

### 11.3 — Line-by-Line Breakdown

**`char *word = get_string("Word: ");`**
The user types a word — e.g. `"Apple"`. The variable `word` stores a pointer to that string.

---

**`printf("Hash value: %i\n", hash(word));`**
Calls the `hash` function with the word and prints the number it returns.

---

**`if (word == NULL || strlen(word) == 0)`**
Safety check before doing anything else. Two failure cases:

| Condition | What it means | Why it matters |
|-----------|--------------|---------------|
| `word == NULL` | Nothing was typed / get_string failed | Indexing into NULL crashes the program |
| `strlen(word) == 0` | Empty string — user just pressed Enter | No first character to read |

Both return `-1` as an error signal.

---

**`char c = word[0];`**
Grabs just the **first character** of the word.

```
word    = "Apple"
word[0] = 'A'      ← c gets this
word[1] = 'p'
word[2] = 'p'
...
```

---

**`if (isalpha(c))`**
Checks whether the first character is actually a **letter** (not a digit, space, or symbol). Words starting with `"3cats"` or `"!hello"` would skip the return and fall through to `return -1`.

---

**`c = toupper(c);`**
Forces the character to uppercase so that lowercase and uppercase letters map to the same bucket.

```
'a'  →  'A'   (lowercase converted)
'B'  →  'B'   (already uppercase, no change)
'z'  →  'Z'
```

Without this, `'a'` (ASCII 97) and `'A'` (ASCII 65) would give different results from `c - 'A'`.

---

**`return c - 'A';`**
The clever core of the function. In C, characters are stored as numbers (ASCII values):

```
'A' = 65
'B' = 66
'C' = 67
...
'Z' = 90
```

Subtracting `'A'` (65) maps every letter to 0–25:

| Letter | ASCII | `- 'A'` | Bucket |
|--------|-------|---------|--------|
| `'A'`  | 65    | 0       | 0      |
| `'B'`  | 66    | 1       | 1      |
| `'C'`  | 67    | 2       | 2      |
| `'Z'`  | 90    | 25      | 25     |

---

**`return -1;`** (at the bottom)
Reached only if the first character was not alphabetical (e.g. the word starts with a digit or symbol). `-1` signals an invalid input.

---

### 11.4 — Full Example Walkthrough: "banana"

```
Input:  "banana"

Step 1: word = "banana"
Step 2: word == NULL?        No
        strlen("banana") == 0? No  → pass safety check
Step 3: c = word[0] = 'b'
Step 4: isalpha('b')?        Yes
Step 5: toupper('b') → 'B'
Step 6: 'B' - 'A' = 66 - 65 = 1
Step 7: return 1

Result: "banana" → bucket 1 (the B cabinet) ✅
```

Another example: `"Zebra"`:
```
c = 'Z'  (already uppercase after toupper)
'Z' - 'A' = 90 - 65 = 25
→ bucket 25 ✅
```

---

### 11.5 — Why This Hash Function Is Weak

The weakness: **every word starting with the same letter gets the same bucket**, causing massive pileups.

```
Bucket 1 (B):  ball, banana, bear, book, butter, bridge, billion, blue, bright...
Bucket 8 (I):  (nearly empty — few common words start with I)
Bucket 23 (X): (probably empty — almost no common English words start with X)
```

This creates an **uneven distribution** — some buckets overflow while others sit empty. A long bucket means slow search (you still have to scan every node in it).

---

### 11.6 — How to Improve the Hash Function

| Improvement | How | Effect |
|-------------|-----|--------|
| Use all characters, not just the first | Sum ASCII values of every letter | Words with same first letter but different bodies spread out |
| Consider word length | Include `strlen(word)` in the calculation | More variety in the output |
| Use more buckets | 676 buckets (26×26) based on first two letters | Collisions drop dramatically |

**Improved version — sum of all characters:**

```c
int hash(char *word)
{
    if (word == NULL || strlen(word) == 0)
    {
        return -1;
    }

    int total = 0;
    for (int i = 0; word[i] != '\0'; i++)
    {
        if (isalpha(word[i]))
        {
            total += toupper(word[i]) - 'A';
        }
    }
    return total % 26;   // keep within 0–25 range
}
```

**Why this is better:**

```
"ball":   B(1) + A(0) + L(11) + L(11) = 23  → bucket 23
"bear":   B(1) + E(4) + A(0)  + R(17) = 22  → bucket 22
"book":   B(1) + O(14) + O(14) + K(10) = 39 → 39 % 26 = 13

Now ball, bear, and book are in DIFFERENT buckets — no collision! ✅
```

Compare to the first-letter hash where all three would collide into bucket 1.

- **Clarification — `% 26` (modulo):** The sum of all characters can exceed 25. The `%` operator gives you the remainder after dividing by 26, keeping the result within 0–25 no matter how large the sum gets. For example, `39 % 26 = 13` because 26 goes into 39 once with 13 left over.

---

## 13. 🚀 Improving the Hash Function

### Why the first-letter hash is weak

| Problem | Example |
|---------|---------|
| **Uneven distribution** | English has many more H-words than X-words → H bucket gets very long |
| **Empty buckets** | Practically no English words start with X, Q, or Z → wasted memory |
| **Lots of collisions** | "hello", "hey", "hi", "here", "her", "he" all collide into the H bucket |

### Attempt 1 — Map by first two letters (simple addition)

Idea: use both the first and second letter.

```c
return (toupper(word[0]) - 'A') + (toupper(word[1]) - 'A');
```

**Problem:** This still causes collisions.

```
"AC" → 0 + 2 = 2
"BB" → 1 + 1 = 2   ← same bucket! collision ❌
```

Different letter combinations produce the same sum.

### Attempt 2 — Weighted two-letter hash (better)

Multiply the first letter's value by 26 (or any constant ≥ 26) before adding the second:

```c
int h1 = toupper(word[0]) - 'A';   // 0–25
int h2 = toupper(word[1]) - 'A';   // 0–25
return h1 * 26 + h2;
```

```
"AC" → 0 * 26 + 2 = 2
"BB" → 1 * 26 + 1 = 27   ← different bucket ✅
"CA" → 2 * 26 + 0 = 52   ← different again ✅
```

Every unique two-letter prefix now maps to a unique bucket. This requires an array of size 26² = 676 buckets.

### Attempt 3 — Sum of all characters (common approach)

```c
int hash_value = 0;
for (int i = 0; word[i] != '\0'; i++)
{
    hash_value += toupper(word[i]) - 'A';
}
return hash_value % NUM_BUCKETS;   // keep within array bounds
```

Uses the entire word instead of just the first one or two letters. Still has collisions (anagrams like "abc" and "bca" hash the same), but distributes much more evenly across buckets.

### Properties of a good hash function

| Property | What it means |
|----------|--------------|
| **Deterministic** | Same input always gives the same output — no randomness |
| **Even distribution** | Entries spread uniformly across all buckets — no bucket is much longer than others |
| **Uses all buckets** | As few empty buckets as possible — no wasted memory |
| **Fast to compute** | The hash function itself should be O(1) or very close to it |

---

## 14. 📌 Summary

| Concept | Detail |
|---------|--------|
| **Choose a data structure by** | What operations you need (search/insert/delete) and which must be fastest |
| **Linked list** | Chain of nodes — O(n) search, O(1) prepend, dynamic size |
| **Hash table** | Array of linked lists — O(1) best-case search, more memory |
| **Trie** | Tree of arrays — O(1) always, very high memory |
| **Time vs space** | The central trade-off — faster search costs more memory |
| **Node struct** | `int number; struct node *next;` — forward reference requires `struct node` name |
| **Prepend insertion** | `n->next = list; list = n;` — order matters; new node's next must be set first |
| **Why list prints reversed** | Prepend puts newest node at front — reading from head gives LIFO order |
| **Freeing a list** | Save `next` before `free` — never access a pointer after freeing it |
| **Hash function** | Maps a word to a bucket index; must be deterministic and evenly distributed |
| **First-letter hash weakness** | Uneven distribution; many collisions for common starting letters |
| **Two-letter weighted hash** | `h1 * 26 + h2` — gives unique bucket per two-letter prefix |
| **Good hash function** | Deterministic, evenly distributed, uses all buckets, fast to compute |
