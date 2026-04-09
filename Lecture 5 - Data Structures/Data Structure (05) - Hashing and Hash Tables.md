#️⃣ Hashing and Hash Tables (Lecture 5 — Data Structures @ CS50)

After arrays, linked lists, and trees, we're still chasing one goal: **O(1) — constant time search**. No matter how many elements are in the structure, finding any one of them takes the same number of steps. Hash tables are the closest thing to achieving this in practice. They are, as Professor Malan puts it, "the Swiss Army knife of data structures."

---

## 1. 🏆 The Holy Grail — O(1) Constant Time

Looking at our complexity graph:

```
Steps
  │
  │  O(n)   — linear (arrays unsorted, linked lists)
  │   \
  │    \  O(log n) — logarithmic (binary search, balanced BST)
  │     \___
  │         \___________  O(1) — constant (the goal!)
  └─────────────────────────────── Elements (n)
```

O(1) means: it doesn't matter if your contact list has 100 people or 100 million people — finding someone takes the same number of steps. This is how fast search feels when it works well (Google, iPhone contacts).

---

## 2. 🧠 What Is Hashing?

**Hashing** is the idea of taking an input value from a large (or infinite) domain and mapping it to an output from a small, finite range.

```
Domain (infinite):    Any name in the world
Range (finite):       0–25 (one bucket per letter of the alphabet)

"Mario"  → hash function → 12  (M is the 13th letter → index 12)
"Luigi"  → hash function → 11  (L is the 12th letter → index 11)
"Peach"  → hash function → 15  (P is the 16th letter → index 15)
```

- **Clarification — domain and range:** From high school math: *domain* is the set of all possible inputs, *range* is the set of all possible outputs. Hashing converts a large/infinite domain (all possible names) to a small finite range (26 buckets), so we can put data into a predictable, indexed slot.

Real-world analogy: sorting a deck of 52 playing cards by suit first. You go from a 52-card problem to 4 smaller piles (♠ ♥ ♦ ♣) — each pile is smaller and easier to sort. The suit is your "hash value."

---

## 3. 🔧 What Is a Hash Function?

A **hash function** is an algorithm that:
- **Takes** an item (e.g. a name string) as input
- **Returns** an integer index (e.g. 0–25) that tells you which bucket to put it in

A simple hash function for names based on the first letter:

```c
#include <ctype.h>

unsigned int hash(const char *word)
{
    return toupper(word[0]) - 'A';
}
```

### Breaking this down line by line

| Part | What it does |
|------|-------------|
| `unsigned int` | Return type is always non-negative (array indices can't be negative) |
| `const char *word` | Takes a string; `const` means we promise not to modify it |
| `toupper(word[0])` | Look at the first character, force it to uppercase (so 'a' and 'A' both work) |
| `- 'A'` | Subtract the ASCII value of 'A' (65), so 'A'→0, 'B'→1, ... 'Z'→25 |

```
"Mario"  → toupper('M') = 'M' = 77 → 77 - 65 = 12 ✅
"Luigi"  → toupper('L') = 'L' = 76 → 76 - 65 = 11 ✅
"Zelda"  → toupper('Z') = 'Z' = 90 → 90 - 65 = 25 ✅
```

This function always produces a value between 0 and 25 — perfect for indexing into an array of 26 buckets.

---

## 4. 🗃️ What Is a Hash Table?

A **hash table** is an **array of pointers to linked lists**. It's a mashup of two structures we already know:

```
Hash table = Array (for instant bucket lookup by index)
           + Linked List (for each bucket, to handle multiple names)
```

Visualised as 26 buckets (one per letter):

```
Index │ Bucket
──────┼────────────────────────────────────────
  0   │  NULL                     (no A-names)
  1   │  NULL                     (no B-names)
  ...
 11   │  [Luigi] → [Link] → NULL  (L-names)
 12   │  [Mario] → NULL           (M-names)
 ...
 15   │  [Peach] → NULL           (P-names)
 ...
 25   │  [Zelda] → NULL           (Z-names)
```

In code, a hash table is just an array of node pointers (initially all NULL):

```c
// Define the node (what goes inside each bucket's linked list)
typedef struct node
{
    char name[50];       // the name we're storing
    struct node *next;   // pointer to next node in the same bucket
}
node;

// The hash table itself: an array of 26 pointers to nodes
node *table[26];
```

Each slot in the array is a pointer to the start of a linked list. When you insert a name, you:
1. Run the hash function to find which bucket it belongs in
2. Insert the new node into that bucket's linked list

---

## 5. 💥 Collisions — When Two Values Hash to the Same Bucket

What happens when two names start with the same letter? For example, both "Mario" and "Moana" hash to index 12 (M).

This is called a **collision** — two different values mapping to the same bucket.

### Solution: Chaining

Don't throw away the old value. Just build a **linked list** in that bucket:

```
Before (only Mario):
  12  │  [Mario|NULL]

After inserting Moana (collision!):
  12  │  [Mario] → [Moana|NULL]
```

```
Searching for "Moana" in bucket 12:
  → hash("Moana") = 12
  → Check [Mario] — not a match
  → Follow the pointer to [Moana] — match! ✅
```

This strategy is called **separate chaining**: each bucket holds a chain (linked list) of all values that hash to that bucket.

---

## 6. 🧮 A Better Hash Function — Fewer Collisions

The first-letter hash puts every M-name in the same bucket, every L-name in the same bucket, etc. In a large dataset, popular starting letters (like M, S, J) would have very long chains.

A better approach: use the first **3 letters** instead of just 1. Now "LUI" and "LIN" are different buckets — Luigi and Link no longer collide.

```
"Luigi" → hash("LUI") → unique bucket
"Link"  → hash("LIN") → different unique bucket
```

This requires a **much bigger array** — 26³ = 17,576 possible three-letter combinations — but most of those slots will be empty (no Nintendo character starts with "AAA" or "ZZZ"). You're trading memory for fewer collisions and faster search.

> **The fundamental tension:** The larger your array (more buckets), the fewer collisions, the faster the search — but the more memory you waste on empty buckets. A good hash function finds the right balance.

---

## 7. ⏱️ Time Complexity of Hash Tables

| Case | Time | Explanation |
|------|------|-------------|
| **Best case** | O(1) | Hash to an empty bucket — one step |
| **Average case** | O(n/k) | n names spread across k buckets — short chains |
| **Worst case** | O(n) | Every name hashes to the same bucket — one giant chain |

- **Clarification — O(n/k) vs O(1):** If you have 260 names and 26 buckets, each bucket has on average 10 names. Searching takes 10 steps instead of 260 — much faster! But 10 is still proportional to n (the total names), so technically it's still O(n). In practice though, with a good hash function and many buckets, it *feels* like O(1) because the chains are so short. Whether you call it O(1) or O(n) depends on how strict you want to be mathematically.

---

## 8. 🌍 Real-World Hash Table — Sweetgreen Restaurant

Professor Malan's real-world example: at the Sweetgreen restaurant in Harvard Square, pickup orders are stored on a physical shelf with buckets by name range (A–E, F–J, K–N, O–Z). Your order is "hashed" into a bucket based on your name, so staff can find it instantly without searching the whole shelf. This is a real hash table — in physical form.

---

## 9. ⚖️ Hash Table vs. Previous Structures

| Structure | Search | Growth | Memory |
|-----------|--------|--------|--------|
| Unsorted array | O(n) | Hard (copy) | Low |
| Sorted array | O(log n) | Hard (copy) | Low |
| Linked list | O(n) | Easy | Medium |
| BST (balanced) | O(log n) | Easy | Medium-high |
| **Hash table** | **O(1) best / O(n) worst** | **Easy** | **Medium-high** |

Hash tables beat everything else in the best case, at the cost of extra memory and a potential worst case that's no better than a linked list (if all keys collide).

---

## 10. 📌 Summary

| Concept | Detail |
|---------|--------|
| **Hashing** | Converting an input (e.g. a name) into an integer index (e.g. 0–25) |
| **Hash function** | The algorithm that performs the conversion |
| **Hash table** | An array of linked lists — one linked list per bucket |
| **Bucket** | One slot in the hash table array — holds a linked list of colliding items |
| **Collision** | Two different keys hashing to the same bucket |
| **Separate chaining** | Handling collisions by building a linked list in each bucket |
| **Best-case search** | O(1) — jump to bucket, find item immediately |
| **Worst-case search** | O(n) — all items collide into one giant chain |
| **Good hash function** | Distributes keys evenly across all buckets — minimises collisions |
| **Bigger array** | Fewer collisions, faster search, but more memory wasted on empty buckets |
| **Real-world use** | Phone contacts, dictionaries, Sweetgreen salad pickup shelves |
