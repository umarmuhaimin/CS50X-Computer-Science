#️⃣ Hash Tables (Lecture 5 — Data Structures @ CS50 | Doug Lloyd)

A hash table is arguably the most powerful general-purpose data structure you'll encounter in CS50. It combines the random access speed of an array with the dynamic growth of a linked list — and when implemented correctly, achieves close to constant time for insertion, deletion, and lookup. This note covers everything from the core concept to collisions and the two methods for resolving them.

---

## 1. 🧠 What Is a Hash Table? — The Mashup Concept

So far you have seen arrays and linked lists, and you understand their trade-offs:

| | Array | Linked List |
|--|-------|------------|
| **Access speed** | O(1) — jump directly to any index | O(n) — must traverse from head |
| **Resizing** | Fixed at declaration | Grows/shrinks dynamically |
| **Insert/Delete** | O(n) — shift elements | O(1) — just update pointers |

A hash table takes the best of both:

```
Hash Table = Array (random access) + Linked List (dynamic growth)
```

The key insight: **let the data itself tell you where it belongs in the structure.** When you want to find it later, ask the data again — and it'll point you straight there.

---

## 2. ⚡ Why Hash Tables — Performance Goals

A well-implemented hash table can achieve:

| Operation | Average case |
|-----------|-------------|
| **Insertion** | Θ(1) — constant time |
| **Deletion** | Θ(1) — constant time |
| **Lookup** | Θ(1) — constant time |

> **Θ (Theta)** notation means the *average case* — what actually happens in typical use, not the best or worst case. Hash tables approach constant time on average.

### The one trade-off

Hash tables are **poor at ordering or sorting data**. If you start using them to sort, all the performance gains disappear and you degrade to Θ(n) — essentially a linked list. Only use hash tables when you don't need your data sorted.

---

## 3. 🔧 The Two Components

A hash table is built from exactly two pieces:

### Component 1 — The Hash Function

A hash function takes data as input and returns a **non-negative integer** called a **hash code**.

```
data  ──► [ hash function ] ──► hash code (a non-negative integer)
"John" ──► [   hash()    ] ──► 4
"Paul" ──► [   hash()    ] ──► 6
```

### Component 2 — The Array

An array capable of storing the data type you want to insert. The hash code becomes the **index** into this array.

```c
string hashtable[10];   // 10 slots, indexed 0–9
```

```
Index │ Data
──────┼──────────
  0   │ (empty)
  1   │ (empty)
  2   │ (empty)
  3   │ (empty)
  4   │ "John"     ← hash("John") returned 4
  5   │ (empty)
  6   │ "Paul"     ← hash("Paul") returned 6
  7   │ (empty)
  8   │ (empty)
  9   │ (empty)
```

---

## 4. 📐 How It Works — The Core Mechanism

The entire logic of a hash table comes down to two operations:

### Inserting data
```
1. Take the data ("John")
2. Run it through the hash function → get a hash code (4)
3. Store the data at array[hashcode] → hashtable[4] = "John"
```

### Looking up data
```
1. Take the same data ("John")
2. Run it through the same hash function → get the same code (4)
3. Jump directly to array[4] → "John" is there ✅ (or not ❌)
```

This is why **the hash function must be deterministic**: the same input must always produce the same output. Otherwise you'd store at one index but look up at a different one — and never find anything.

---

## 5. 💻 Visualising Insertion — John and Paul

**Starting state:** Empty 10-slot hash table.

```c
string hashtable[10];
```

```
  0  [        ]
  1  [        ]
  2  [        ]
  3  [        ]
  4  [        ]
  5  [        ]
  6  [        ]
  7  [        ]
  8  [        ]
  9  [        ]
```

---

**`int x = hash("John");  // x is now 4`**
**`hashtable[x] = "John";`**

```
  0  [        ]
  1  [        ]
  2  [        ]
  3  [        ]
  4  [ "John" ]  ← stored at index 4
  5  [        ]
  6  [        ]
  7  [        ]
  8  [        ]
  9  [        ]
```

---

**`int y = hash("Paul");  // y is now 6`**
**`hashtable[y] = "Paul";`**

```
  0  [        ]
  1  [        ]
  2  [        ]
  3  [        ]
  4  [ "John" ]
  5  [        ]
  6  [ "Paul" ]  ← stored at index 6
  7  [        ]
  8  [        ]
  9  [        ]
```

**Lookup is instant:** `hash("John")` → 4 → check `hashtable[4]` → found. No scanning needed. That's the power.

---

## 6. 🎯 What Makes a Good Hash Function?

There is no limit to the number of possible hash functions — and many bad ones exist. Here are the five properties every good hash function must have:

| Property | What it means | Why it matters |
|----------|--------------|----------------|
| **Use only the data being hashed** | Don't incorporate anything external (random numbers, timestamps) | Ensures reproducibility |
| **Use ALL of the data being hashed** | Don't just look at the first character — use the whole input | Maximises distribution |
| **Be deterministic** | Same input → always same output, no exceptions | Without this, lookup fails — you'd store at one index, look at another |
| **Uniformly distribute data** | Spread hash codes across all possible indices | Prevents crowding; keeps chains short |
| **Generate very different codes for similar data** | "John" and "Jonathan" should land far apart | Reduces collisions between related inputs |

> **Don't write your own hash function for production use.** It's more art than science. The internet is full of excellent, well-tested hash functions. Use one of those — just cite your source.

---

## 7. 💻 Example Hash Function — ASCII Sum

Here is a simple (though not ideal) hash function that sums the ASCII values of all characters in a string:

```c
unsigned int hash(char* str)
{
    int sum = 0;
    for (int j = 0; str[j] != '\0'; j++)
    {
        sum += str[j];   // add ASCII value of each character
    }
    return sum % HASH_MAX;   // mod by array size to stay in bounds
}
```

### Breaking this down line by line

| Line | What it does |
|------|-------------|
| `int sum = 0;` | Start the running total at 0 |
| `str[j] != '\0'` | Loop until the null terminator — detects end of string without needing `strlen` |
| `sum += str[j];` | Add the ASCII value of each character (e.g. 'J'=74, 'o'=111, 'h'=104, 'n'=110) |
| `return sum % HASH_MAX;` | Mod by array size to guarantee the code is a valid index (0 to HASH_MAX-1) |

### Why `% HASH_MAX`?

If your array has 10 slots (indices 0–9), you cannot store data at index 11 or 432 — that would be an out-of-bounds access and cause a segmentation fault. Modulo keeps the hash code within the valid range:

```
sum = 399       HASH_MAX = 10
399 % 10 = 9    → valid index ✅

sum = 432       HASH_MAX = 10
432 % 10 = 2    → valid index ✅
```

### Why this particular function isn't ideal

Anagrams like "act" and "cat" have the same ASCII sum → same hash code → guaranteed collision. A better function would account for *position*, not just the letters.

---

## 8. 💥 The Problem: Collisions

A **collision** occurs when two different pieces of data, run through the same hash function, produce the **same hash code**.

```
hash("Paul")  → 6
hash("Ringo") → 6   ← COLLISION! Both map to slot 6.
```

We can't simply overwrite Paul with Ringo — both need to be in the table. We need a way to store both at the same index.

There are two strategies: **linear probing** and **chaining**.

---

## 9. 🔀 Resolving Collisions: Linear Probing

**Linear probing:** if the intended slot is occupied, try the next slot. If that's full, try the one after. Keep going (wrapping around to index 0 if needed) until you find an empty slot.

```
hash("Ringo") → 6, but 6 is taken
→ try slot 7 → empty → put Ringo at 7
```

### Visual walkthrough — The Simpsons (hash function returns 6 for Bart, Lisa, Marge)

**Step 1 — `hash("Bart") = 6`** → slot 6 is empty → place Bart at 6

```
  6  [ "Bart" ]   ← placed directly
```

**Step 2 — `hash("Lisa") = 6`** → slot 6 is full → try 7 → empty → place Lisa at 7

```
  6  [ "Bart" ]
  7  [ "Lisa" ]   ← collision resolved, moved to 7
```

**Step 3 — `hash("Homer") = 7`** → slot 7 is full → try 8 → empty → place Homer at 8

```
  6  [ "Bart" ]
  7  [ "Lisa" ]
  8  [ "Homer" ]  ← collision resolved, moved to 8
```

**Step 4 — `hash("Maggie") = 3`** → slot 3 is empty → place directly

```
  3  [ "Maggie" ]
  6  [ "Bart"   ]
  7  [ "Lisa"   ]
  8  [ "Homer"  ]
```

**Step 5 — `hash("Marge") = 6`** → 6 full, 7 full, 8 full, 9 empty → place Marge at 9

```
  3  [ "Maggie" ]
  6  [ "Bart"   ]
  7  [ "Lisa"   ]
  8  [ "Homer"  ]
  9  [ "Marge"  ]  ← had to probe 3 slots past intended position!
```

Marge ended up three slots away from where she was supposed to be. When looking her up, you'd have to check 6 (Bart), 7 (Lisa), 8 (Homer), 9 (Marge) — four checks, not one.

---

## 10. 📊 The Problem With Linear Probing: Clustering

Linear probing introduces a serious problem called **clustering**.

```
Before collision:
  6  [ "Bart" ]
  7  [ empty  ]

After collision (Lisa goes to 7):
  6  [ "Bart" ]
  7  [ "Lisa" ]   ← now two adjacent slots filled
```

Once two adjacent cells are occupied, **the probability of a new collision hitting that cluster doubles**. The cluster grows, attracting more collisions, growing further, until:

```
  6  [ "Bart"   ]  ← all 4 slots filled from one hash code cluster
  7  [ "Lisa"   ]
  8  [ "Homer"  ]
  9  [ "Marge"  ]
```

As the table fills up, you end up probing many slots per insertion or lookup — and the average case degrades from Θ(1) toward Θ(n). You've lost the main advantage of hash tables.

### The second problem: fixed capacity

Even with the best probing strategy, a plain array can only hold as many elements as its declared size. If you have 10 slots and try to insert an 11th element, there's nowhere to put it. You can end up in an infinite loop searching for an empty slot that doesn't exist.

---

## 11. 🔗 Resolving Collisions: Chaining — The Better Way

**Chaining** solves both problems at once by changing what each array slot holds.

Instead of each slot storing one piece of data:
```c
string hashtable[10];       // stores strings directly
```

Each slot stores a **pointer to the head of a linked list**:
```c
node* hashtable[10];        // stores pointers to linked list nodes
```

```
Before chaining:
  6  [ "Bart" ]             ← can only hold ONE string

After chaining:
  6  [ ──────►] ──► ["Bart" | NULL]   ← pointer to a linked list
```

Now each bucket can hold **as many elements as needed** by growing its linked list.

### Why chaining solves everything

| Problem | Linear Probing | Chaining |
|---------|---------------|---------|
| **Collision** | Probe for next empty slot | Prepend to linked list at same index |
| **Clustering** | Happens — clusters grow | Eliminated — each bucket is independent |
| **Fixed capacity** | Yes — limited to array size | No — each chain grows dynamically |
| **Insertion** | O(1) to O(n) depending on probing | O(1) — always prepend to list head |
| **Lookup** | O(n) in worst case (cluster) | O(n/k) where k = number of buckets |

### The math behind chaining's speed

If you have n total elements spread evenly across k buckets:
- Each chain has approximately **n/k** elements
- Lookup requires searching one chain: O(n/k)
- With k=10: 10× faster than a single list
- With k=1000: 1000× faster than a single list

In practice (with a good hash function), this feels close to O(1) even though it's technically O(n/k).

---

## 12. 💻 Chaining Walkthrough — Friends (with collision)

**Hash table:** `node* hashtable[10];` — all 10 pointers start as NULL.

```
  0  [NULL]
  1  [NULL]
  2  [NULL]
  3  [NULL]
  4  [NULL]
  5  [NULL]
  6  [NULL]
  7  [NULL]
  8  [NULL]
  9  [NULL]
```

---

**`hash("Joey") = 6`** — malloc a new node for "Joey", point slot 6 to it:

```
  6  [──────►] ──► [ "Joey" | NULL ]
```

---

**`hash("Ross") = 2`** — malloc a new node for "Ross", point slot 2 to it:

```
  2  [──────►] ──► [ "Ross" | NULL ]
  6  [──────►] ──► [ "Joey" | NULL ]
```

---

**`hash("Rachel") = 4`** — malloc a new node for "Rachel", point slot 4 to it:

```
  2  [──────►] ──► [ "Ross"   | NULL ]
  4  [──────►] ──► [ "Rachel" | NULL ]
  6  [──────►] ──► [ "Joey"   | NULL ]
```

---

**`hash("Phoebe") = 6`** — COLLISION! Slot 6 already has "Joey". With chaining, this is easy:

```
Step 1: malloc a new node for "Phoebe"
Step 2: new->next = hashtable[6]   (Phoebe's next → Joey)
Step 3: hashtable[6] = new         (slot 6 now points to Phoebe)
```

```
  2  [──────►] ──► [ "Ross"   | NULL ]
  4  [──────►] ──► [ "Rachel" | NULL ]
  6  [──────►] ──► [ "Phoebe" | ──►] ──► [ "Joey" | NULL ]
```

Phoebe is prepended to the chain. No clustering, no probing, no limit on how many elements share the same hash code. ✅

### Looking up "Joey" after chaining:

```
1. hash("Joey") → 6
2. Go to hashtable[6] → head of chain
3. Check first node: "Phoebe" ≠ "Joey" → advance
4. Check second node: "Joey" == "Joey" → found ✅
```

Two checks instead of one, but still much faster than scanning the whole dataset.

---

## 13. ⚖️ Linear Probing vs. Chaining

| | Linear Probing | Chaining |
|--|---------------|---------|
| **Storage** | Data stored directly in array slots | Data stored in linked list nodes (heap-allocated) |
| **Collision handling** | Probe to next available slot | Prepend to linked list at same index |
| **Clustering** | ✅ Yes — a major problem | ❌ No — each chain is independent |
| **Capacity limit** | Fixed at array size | Unlimited — chains grow dynamically |
| **Memory overhead** | Low — no extra pointers | Higher — each node needs a `next` pointer |
| **Lookup worst case** | O(n) — entire table probed | O(n) — entire chain searched |
| **Lookup average case** | Degrades as table fills | O(n/k) — consistently fast |
| **Recommended?** | Not for large datasets | ✅ Yes — the preferred method |

---

## 14. 📌 Summary

| Concept | Detail |
|---------|--------|
| **Hash table** | Array + linked list (chaining) or just array (linear probing) |
| **Hash function** | Converts data → non-negative integer (hash code) |
| **Hash code** | The index into the array where data is stored |
| **Average case** | Θ(1) for insert, delete, lookup — with a good hash function |
| **Trade-off** | Poor at sorting/ordering data |
| **Deterministic** | Same input → always same output, always |
| **Collision** | Two different inputs produce the same hash code |
| **Linear probing** | On collision: try next slot, then next, etc. |
| **Clustering** | Adjacent collisions snowball — degrades performance |
| **Chaining** | Each slot holds a pointer to a linked list; collisions just extend the chain |
| **Array declaration (simple)** | `string hashtable[10];` — one item per slot |
| **Array declaration (chaining)** | `node* hashtable[10];` — pointer to linked list per slot |
| **Chaining insert** | Prepend to linked list at `hashtable[hash(data)]` — always O(1) |
| **Chaining lookup** | Hash → go to bucket → linear search of that chain |
| **Why chaining wins** | No clustering, no capacity limit, consistently fast lookup |
| **Lookup speed with k buckets** | O(n/k) — 1000 buckets = 1000× faster than one big list |

### The hash table workflow in two lines

```
Insert:  hashtable[hash(data)] = data;              // simple array
         prepend(data, hashtable[hash(data)]);       // chaining

Lookup:  if hashtable[hash(data)] == data → found   // simple array
         search linked list at hashtable[hash(data)] // chaining
```

### Chaining visualised

```
node* hashtable[10]:

  0  [NULL]
  1  [NULL]
  2  [──►] ──► [ "Ross"   | NULL ]
  3  [NULL]
  4  [──►] ──► [ "Rachel" | NULL ]
  5  [NULL]
  6  [──►] ──► [ "Phoebe" | ──►] ──► [ "Joey" | NULL ]
  7  [NULL]
  8  [NULL]
  9  [NULL]

To find "Joey":  hash("Joey") = 6 → scan chain at slot 6 → found in 2 steps
To find "Ross":  hash("Ross") = 2 → scan chain at slot 2 → found in 1 step
```
