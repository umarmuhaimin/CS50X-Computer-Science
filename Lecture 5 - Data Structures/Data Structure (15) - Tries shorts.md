🌳 Tries (Lecture 5 — Data Structures @ CS50 | Doug Lloyd)

Tries are described by Doug Lloyd as the closest thing computer science has to a "holy grail" data structure — one that achieves constant-time insertion, deletion, and lookup. The catch is that they consume massive amounts of memory. This note covers everything: what a trie is, how nodes are structured, how to insert and search, why it achieves O(1), and what you give up to get it.

---

## 1. 🧠 What Is a Trie?

A **trie** (pronounced "try", short for *re**trie**val*) is a tree-like data structure that stores data by using the **data itself as a roadmap** to navigate the structure.

The fundamental rule:

> **If you can follow the data's path from the root to the end — the data exists. If you hit a dead end (a NULL pointer) at any point — it doesn't.**

Unlike hash tables, tries **never have collisions**. Every unique piece of data has a unique path through the trie. No two different keys share the same complete route (unless the data is literally identical).

### The analogy — a tree flipped upside down

Think of a real tree: roots in the ground, branches spreading upward, leaves at the tips. A trie is the same idea, just **inverted** — the root is at the top, and the leaves (where your data lives) are at the bottom. Each branch you follow is labeled with a digit (or character), telling you which direction to go at each step.

```
             [ROOT]
           /   |   \
         [1]  [2]  ...  [9]       ← first digit
        /   \
      [6]  [7]                    ← second digit
      /       \
    [3]       [0]                 ← third digit
    /             \
  [6]             [1]             ← fourth digit
   ↓               ↓
"Harvard"        "Yale"           ← value stored at leaf
```

---

## 2. 🗝️ Key-Value Pairs — Comparing Data Structures

Tries are the third key-value structure we've seen. Here's how all three compare:

| Structure | Key | Value |
|-----------|-----|-------|
| **Array** | Element index (0, 1, 2...) | Data stored at that index |
| **Hash table** | Hash code of the data | Linked list of items hashing to that code |
| **Trie** | The data itself (used digit-by-digit as a path) | A Boolean (or data) at the end of the path |

The critical difference with a trie: **the key is guaranteed unique**. In a hash table, two different items can share the same hash code (collision) and you have to resolve it. In a trie, two different paths can never be the same unless the underlying data is identical.

---

## 3. 🗺️ The Core Concept — Data as a Roadmap

The example in this short maps **university founding years (YYYY)** to **university names**:

```
Key:   1636   →   Value: "Harvard"
Key:   1701   →   Value: "Yale"
Key:   1769   →   Value: "Dartmouth"
```

The year is a 4-digit number. Each digit (0–9) tells us which branch to follow at each level of the trie. Since there are 10 possible digits, every node in the trie has **10 pointers** — one for each possible digit (0 through 9).

```
Navigation: 1636 means:
  Start at root
  → Follow pointer [1]   (first digit)
  → Follow pointer [6]   (second digit)
  → Follow pointer [3]   (third digit)
  → Follow pointer [6]   (fourth digit)
  → Look at the value here: "Harvard" ✅
```

If at any step the pointer for your next digit is NULL — meaning that path was never built — you immediately know the data doesn't exist in the trie.

---

## 4. 🏗️ The Node Struct

```c
typedef struct _trie
{
    char university[20];    // the value stored at this node (the university name)
    struct _trie* paths[10]; // 10 pointers, one per digit 0–9
}
trie;
```

### Breaking this down

| Field | Type | Purpose |
|-------|------|---------|
| `university[20]` | `char[]` | The data stored at this node — the university name (empty unless this is a leaf) |
| `paths[10]` | `struct _trie*[10]` | Array of 10 pointers — one for each digit 0–9 |

- **Clarification — `paths[10]`:** `paths[0]` is the pointer you follow when the current digit is 0. `paths[1]` is for digit 1. `paths[9]` is for digit 9. Initially all 10 are NULL — no paths exist yet.
- **Clarification — `university[20]`:** This field is only meaningful at a **leaf node** (the end of a complete key). At intermediate nodes, it's empty — those nodes just exist to connect the path.
- **Clarification — not just Boolean:** The transcript mentions a value "as simple as a Boolean." In this example, we store the full university name. In some trie uses (like spell-checkers), the value really is just `true`/`false` — confirming the word exists.

---

## 5. 📐 What a Node Looks Like in Memory

From the slides, each node is drawn as:

```
┌──────────────────────────────────┐
│  university: ""                  │  ← blue bar: the stored value (empty = intermediate node)
├──┬──┬──┬──┬──┬──┬──┬──┬──┬──┤
│  │  │  │  │  │  │  │  │  │  │  ← 10 red squares: paths[0] through paths[9], all NULL
│0 │1 │2 │3 │4 │5 │6 │7 │8 │9 │
└──┴──┴──┴──┴──┴──┴──┴──┴──┴──┘
```

- **Red square** = `paths[i]` is NULL — path doesn't exist yet
- **Green square** = `paths[i]` is non-NULL — path has been built (malloc'd)
- **Blue bar** = the value stored at this node (empty string if not a leaf)

### Initialising the root

```c
trie *root = malloc(sizeof(trie));
// All paths[0..9] initialised to NULL
// university field initialised to ""
```

The root is usually declared as a **global pointer** that you never overwrite. For traversal, you create a separate pointer (`trav`) and use that to navigate.

---

## 6. 🔧 Inserting — Harvard (1636)

**Goal:** Insert "Harvard" using key `1636`.

**Starting state:** Root node exists. All 10 paths are NULL (red).

```
ROOT:
university: ""
[0][1][2][3][4][5][6][7][8][9]
 ■  ■  ■  ■  ■  ■  ■  ■  ■  ■    ← all NULL
```

---

**Digit 1 — follow `paths[1]`:** NULL → must malloc a new node.

```
ROOT →[1]→ NEW NODE A (empty, all paths NULL)
```

```
ROOT:
[0][1][2][3][4][5][6][7][8][9]
 ■  ↓  ■  ■  ■  ■  ■  ■  ■  ■    ← [1] now points to Node A

NODE A:
university: ""
[0][1][2][3][4][5][6][7][8][9]
 ■  ■  ■  ■  ■  ■  ■  ■  ■  ■
```

---

**Digit 6 — from Node A, follow `paths[6]`:** NULL → malloc Node B.

```
ROOT →[1]→ NODE A →[6]→ NODE B (empty)
```

---

**Digit 3 — from Node B, follow `paths[3]`:** NULL → malloc Node C.

```
ROOT →[1]→ NODE A →[6]→ NODE B →[3]→ NODE C (empty)
```

---

**Digit 6 — from Node C, follow `paths[6]`:** NULL → malloc Node D (leaf).

```
ROOT →[1]→ NODE A →[6]→ NODE B →[3]→ NODE C →[6]→ NODE D
```

**Key exhausted — write "Harvard" at Node D:**

```
NODE D:
university: "Harvard"    ← the leaf!
[0][1][2][3][4][5][6][7][8][9]
 ■  ■  ■  ■  ■  ■  ■  ■  ■  ■   ← all still NULL (no further paths)
```

**Final trie after inserting Harvard:**

```
ROOT
  └──[1]──► NODE A
               └──[6]──► NODE B
                            └──[3]──► NODE C
                                         └──[6]──► "Harvard"
```

---

## 7. 🔧 Inserting Yale (1701) and Dartmouth (1769) — Shared Paths

### Inserting Yale (key: 1701)

**Digit 1 — `paths[1]` from ROOT:** ✅ Already exists (built for Harvard). Just follow it — no malloc needed.

**Digit 7 — `paths[7]` from Node A:** ❌ NULL → malloc Node E.

**Digit 0 — `paths[0]` from Node E:** ❌ NULL → malloc Node F.

**Digit 1 — `paths[1]` from Node F:** ❌ NULL → malloc Node G (leaf).

**Write "Yale" at Node G.**

```
ROOT
  └──[1]──► NODE A
               ├──[6]──► NODE B → NODE C →[6]──► "Harvard"
               └──[7]──► NODE E            ← NEW branch for Yale
                            └──[0]──► NODE F
                                         └──[1]──► "Yale"
```

Notice: the **[1] branch from ROOT** and **Node A** were already there from Harvard. Yale **shared** the first step. This is a key trie advantage — common prefixes share nodes.

---

### Inserting Dartmouth (key: 1769)

**Digit 1 — ROOT→[1]:** ✅ Exists → follow.

**Digit 7 — Node A→[7]:** ✅ Exists (built for Yale) → follow.

**Digit 6 — Node E→[6]:** ❌ NULL → malloc Node H.

**Digit 9 — Node H→[9]:** ❌ NULL → malloc Node I (leaf).

**Write "Dartmouth" at Node I.**

```
ROOT
  └──[1]──► NODE A
               ├──[6]──► NODE B →[3]──► NODE C →[6]──► "Harvard"
               └──[7]──► NODE E
                            ├──[0]──► NODE F →[1]──► "Yale"
                            └──[6]──► NODE H          ← NEW
                                         └──[9]──► "Dartmouth"
```

Dartmouth shared the first **two** steps (1 and 7) with Yale. Only the last two digits (6 and 9) needed new nodes.

> **Key insight:** As the trie grows, more paths already exist. Inserting new items becomes progressively faster because more of the path is pre-built. Doug Lloyd notes: *"As our trie gets bigger and bigger, we have to do less work every time to insert new things."*

---

## 8. 🔍 Searching — How to Look Up Data

Searching follows the same navigation logic as inserting, but read-only:

```
1. Start at root, set trav = root
2. For each digit of the key (left to right):
   a. Check if trav->paths[digit] is NULL
      → If NULL: DEAD END → data does not exist ❌
      → If non-NULL: follow the pointer, continue
3. After exhausting all digits:
   a. Look at trav->university
   b. If it matches what you're looking for → FOUND ✅
   c. If it's empty or different → NOT FOUND ❌
```

### Why the final check is necessary

After following the full path, you must verify the value. Consider a dictionary trie where "bat" and "batch" are both stored. Searching for "bat" leads you to a node — but that node might only exist as an intermediate step for "batch", with an empty value. The final check confirms whether a complete word actually ends here.

---

## 9. 🔍 Search Success — Harvard (key: 1636)

```
Start at ROOT.

Digit 1: ROOT→paths[1] = ✅ non-NULL → follow to Node A
Digit 6: Node A→paths[6] = ✅ non-NULL → follow to Node B
Digit 3: Node B→paths[3] = ✅ non-NULL → follow to Node C
Digit 6: Node C→paths[6] = ✅ non-NULL → follow to Node D

Key exhausted. Look at Node D's value:
Node D→university = "Harvard"

Searching for "Harvard"? YES → FOUND ✅
```

Total steps: **4**. Exactly the length of the key.

---

## 10. 🔍 Search Failure — Princeton (key: 1746)

Princeton was never inserted. Key: `1746`.

```
Start at ROOT.

Digit 1: ROOT→paths[1] = ✅ non-NULL → follow to Node A
Digit 7: Node A→paths[7] = ✅ non-NULL → follow to Node E
Digit 4: Node E→paths[4] = ❌ NULL → DEAD END

Princeton was NEVER inserted, so paths[4] from Node E was never built.
→ NOT FOUND ❌
```

```
Trie state at the dead end:

NODE E (the node after "17"):
[0][1][2][3][4][5][6][7][8][9]
 ↓  ■  ■  ■  ■  ■  ↓  ■  ■  ■
 │                  │
Yale              Dartmouth
                  branch
                  
paths[4] = NULL → Princeton doesn't exist
```

We hit a dead end at digit 4. We didn't need to check anything else — the absence of that single pointer is definitive proof Princeton isn't in the trie.

---

## 11. ⏱️ Complexity — Why This Is O(1)

| Operation | Time Complexity | Why |
|-----------|----------------|-----|
| **Insert** | O(k) | k = key length; follow k digits, malloc as needed |
| **Search** | O(k) | k = key length; follow k digits |
| **Delete** | O(k) | k = key length; navigate to node, clear the value |

But here's the key insight:

> **k is a constant.** For a 4-digit year key, k = 4. Always. For a dictionary of English words, k ≤ ~45 (longest English word). It doesn't matter if the trie has 3 entries or 3 million — you always make exactly k comparisons.

This means:
```
O(k) where k is constant = O(1)
```

Doug Lloyd puts it directly: *"If we only ever have to look at 4 things, 4 is just a constant. We really are kind of approaching constant time insertion and constant time lookup."*

Compare this to hash tables, which *tend toward* O(1) on average but can degrade. Tries give you **true** O(1) with fixed-length keys.

---

## 12. ⚖️ The Trade-off — Speed vs Memory

Tries are fast. But they pay for that speed with enormous memory usage.

```
One trie node for 4-digit year keys:
  char university[20]  =  20 bytes
  struct _trie* paths[10] = 10 × 8 bytes = 80 bytes
  Total per node: ~100 bytes

A trie for all 4-digit years (0000–9999):
  10,000 possible years
  But each level has 10^depth possibilities:
  Level 1: up to 10 nodes
  Level 2: up to 100 nodes
  Level 3: up to 1,000 nodes
  Level 4: up to 10,000 nodes
  Worst case: 11,110 nodes × 100 bytes = ~1.1 MB
  (just to store 10,000 entries!)
```

For comparison, the same data in a hash table with chaining might use a few KB.

### Why tries still make sense in certain cases

- When lookup speed is absolutely critical (e.g., autocomplete, routing tables, spell-checkers)
- When keys are naturally fixed-length or short
- When shared prefixes allow significant node reuse
- When the number of distinct entries is large relative to the key space

### When tries are overkill

- When keys are long and varied (most nodes would be mostly NULL)
- When memory is tight
- When hash tables already give acceptable performance

---

## 13. 📌 Summary

| Concept | Detail |
|---------|--------|
| **Trie** | Tree-like structure where the data itself is the key/path |
| **No collisions** | Every unique key has a unique path — guaranteed |
| **Key = roadmap** | Navigate digit-by-digit (or letter-by-letter) from root to leaf |
| **Node struct** | `char value[N]` + `struct _trie* paths[k]` (k pointers per node) |
| **`paths[k]`** | k = number of possible characters (10 for digits, 26 for letters) |
| **NULL pointer** | Dead end — data does not exist in this direction |
| **Non-NULL pointer** | Path exists — safe to follow |
| **Inserting** | Navigate the path; malloc new nodes where NULL; write value at leaf |
| **Searching** | Navigate the path; NULL → not found; reach end → verify value |
| **Shared prefixes** | Nodes with common paths are shared — e.g., Harvard and Yale share the "1" node |
| **Complexity** | O(k) where k = key length; for fixed-length keys → **O(1)** |
| **Time** | Constant time insert, delete, lookup |
| **Space** | Very high — each node has many pointers, most NULL |
| **Trade-off** | Speed at the cost of memory |

### The trie vs hash table at a glance

| | Hash Table | Trie |
|--|-----------|------|
| **Collisions** | Possible — must resolve | Never — paths are unique |
| **Lookup** | Θ(1) average | O(1) guaranteed (fixed keys) |
| **Memory** | Moderate | Very high |
| **Ordering** | Not preserved | Not preserved |
| **Key type** | Any hashable | Composable (digit-by-digit / letter-by-letter) |
| **Scaling** | Degrades if hash function is poor | Doesn't degrade — always k steps |

### Navigation logic in pseudocode

```
INSERT(root, key, value):
    trav = root
    for each character c in key:
        if trav->paths[c] == NULL:
            trav->paths[c] = malloc(new node)
        trav = trav->paths[c]
    trav->value = value

SEARCH(root, key):
    trav = root
    for each character c in key:
        if trav->paths[c] == NULL:
            return NOT FOUND
        trav = trav->paths[c]
    if trav->value is set:
        return FOUND (trav->value)
    else:
        return NOT FOUND
```
