🔤 Tries (Lecture 5 — Data Structures @ CS50)

Hash tables get us close to O(1), but not guaranteed — worst case is still O(n) due to collisions. Is there a data structure that truly achieves **O(1) lookup, always?** Yes — and it's called a **trie** (short for "retrieval," pronounced "try").

---

## 1. 🧠 The Core Idea — A Tree Made of Arrays

A **trie** is a tree where each node is an **array of 26 pointers** (one for each letter of the alphabet). Instead of storing actual values inside nodes, tries store words **implicitly** in the paths through the tree.

- **Clarification — "implicit storage":** The word "TOAD" is never actually stored as the string "TOAD" anywhere in memory. Instead, its presence is indicated by the path T → O → A → D being marked as "a word ends here." This is a completely different approach from every other data structure we've seen.

You can think of each node as a crossroads with 26 roads leading out — one for each letter. To store or find a word, you follow the road for the first letter, then the second, then the third, and so on.

---

## 2. 🏗️ Node Structure

```c
typedef struct node
{
    bool is_word;           // true if a valid word ends at this node
    struct node *children[26]; // one pointer per letter (a=0, b=1, ... z=25)
}
node;
```

Or alternatively, if you want to store data (like a phone number) at the end:

```c
typedef struct node
{
    char *number;              // phone number stored here (NULL if no word ends here)
    struct node *children[26]; // one pointer per letter
}
node;
```

Each node in a trie has **26 pointers** — one for each letter. Most of them will be NULL (no word goes through this node in that direction). The `is_word` boolean (or non-NULL pointer) marks positions where a complete word ends.

---

## 3. 📝 Storing Words — Step by Step

### Storing "TOAD"

Start with an empty trie (root node, all 26 children are NULL):

```
Root:
[ a | b | c | d | e | f | g | h | i | j | k | l | m | n | o | p | q | r | s | T | u | v | w | x | y | z ]
  0    1    2    3    4    5    6    7    8    9   10   11   12   13   14  15   16  17   18  19   20  21   22  23   24  25
                                                                                                ↑
                                                                                         index 19 = 'T'
                                                                                         (currently NULL)
```

**Step 1 — T:** Follow the 'T' pointer (index 19) from root. It's NULL, so create a new node there.

```
Root
  └──[T]──► Node_1 (represents "T")
             [ a | b | ... | o | ... | z ]
                                ↑
                            index 14 = 'O'
                            (currently NULL)
```

**Step 2 — O:** From Node_1, follow the 'O' pointer (index 14). Create a new node.

```
Root
  └──[T]──► Node_1
              └──[O]──► Node_2 (represents "TO")
                          [ a | b | ... | z ]
                            ↑
                         index 0 = 'A'
```

**Step 3 — A:** From Node_2, follow the 'A' pointer (index 0). Create a new node.

```
Root
  └──[T]──► Node_1
              └──[O]──► Node_2
                          └──[A]──► Node_3 (represents "TOA")
                                     [ a | b | ... | d | ... | z ]
                                                      ↑
                                                  index 3 = 'D'
```

**Step 4 — D:** From Node_3, follow the 'D' pointer (index 3). Create Node_4.

**Step 5 — End of word:** Set `Node_4->is_word = true` (or store the phone number here).

```
Root
  └──[T]──► Node_1
              └──[O]──► Node_2
                          └──[A]──► Node_3
                                     └──[D]──► Node_4 (is_word = TRUE ✅)
```

"TOAD" is now stored. 4 nodes were created, each an array of 26 pointers. That's 4 × 26 = 104 pointers just for the word "TOAD."

---

## 4. 📝 Storing a Second Word — Shared Paths

Now store "TOM." Notice that "TOM" and "TOAD" both start with "T" and "O" — so they **share the first two nodes** (Node_1 and Node_2).

**Step 1 — T:** Root['T'] already points to Node_1. Follow it (no new node needed).

**Step 2 — O:** Node_1['O'] already points to Node_2. Follow it (no new node needed).

**Step 3 — M:** From Node_2, follow the 'M' pointer (index 12). It's NULL — create Node_5.

**Step 4 — End of word:** Set `Node_5->is_word = true`.

```
Root
  └──[T]──► Node_1
              └──[O]──► Node_2 (shared by TOAD and TOM!)
                          ├──[A]──► Node_3
                          │          └──[D]──► Node_4 (is_word = TRUE ✅ "TOAD")
                          └──[M]──► Node_5 (is_word = TRUE ✅ "TOM")
```

Words with shared prefixes share nodes — no duplication of the common part.

---

## 5. 🔍 Searching in a Trie — Always O(1)

To look up "TOAD":

```
Step 1: hash('T') → index 19 → Root[19] is not NULL → follow it
Step 2: hash('O') → index 14 → Node_1[14] is not NULL → follow it
Step 3: hash('A') → index 0  → Node_2[0] is not NULL → follow it
Step 4: hash('D') → index 3  → Node_3[3] is not NULL → follow it
Step 5: Node_4->is_word == TRUE → "TOAD" is in the trie! ✅
```

**Total: 4 steps.** Always 4 steps for "TOAD" — whether the trie has 3 words or 3 million words.

This is the key insight: **search time depends on the length of the word being looked up, not on the number of words in the trie.**

- "TOAD" → 4 steps (4 letters)
- "MARIO" → 5 steps (5 letters)
- "TOM" → 3 steps (3 letters)

Since words have a bounded maximum length (no Nintendo character has a 1,000-letter name), the number of steps is effectively **constant** — O(1).

---

## 6. ❌ Word Not Found — How Does It Know?

Two cases where a lookup fails:

**Case 1 — a pointer is NULL along the path:**
```
Looking up "TOYZ":
  T → OK
  O → OK
  Y → Node_2['Y'] is NULL → stop immediately → "TOYZ" not in trie ❌
```

**Case 2 — reached the end but is_word is false:**
```
Looking up "TO" (not a word, but a valid prefix):
  T → OK
  O → OK → Node_2->is_word == FALSE → "TO" is not a word ❌
  (but the path exists — it's a prefix of TOAD and TOM)
```

This distinction between "a prefix exists" and "a complete word exists" is handled entirely by the `is_word` boolean.

---

## 7. ⚖️ The Memory Cost

Tries achieve true O(1) search, but pay for it with **enormous memory usage**.

For each word, you need one node per letter. Each node contains 26 pointers.

```
"TOAD" alone:  4 nodes × 26 pointers × 8 bytes per pointer = 832 bytes
              (just to store a 4-letter word!)

Compare to:
  Array:        4 bytes per char × 5 chars (incl. '\0') = 20 bytes
  Hash table:   ~50 bytes for the struct + 1 pointer = ~58 bytes
```

And most of those 26 pointers in each node are NULL — wasted space. A node in the middle of "TOAD" might only use 1 or 2 of its 26 pointers, leaving the other 24 completely empty.

---

## 8. ⚖️ All Data Structures — Final Comparison

| Structure | Search | Growth | Memory | Binary Search? |
|-----------|--------|--------|--------|---------------|
| Array (sorted) | O(log n) | Hard | Low | ✅ Yes |
| Linked list | O(n) | Easy | Medium | ❌ No |
| BST (balanced) | O(log n) | Easy | Medium | ✅ Yes |
| Hash table | O(1) best / O(n) worst | Easy | Medium-high | ❌ N/A |
| **Trie** | **O(1) always** | Easy | **Very high** | ❌ N/A |

### When to use a trie

| Use case | Good for trie? |
|----------|---------------|
| Autocomplete (phone keyboard suggestions) | ✅ Yes — very fast prefix lookup |
| Spell-checker | ✅ Yes — O(1) lookup per word |
| Storing millions of distinct words with constant lookup | ✅ If memory isn't a constraint |
| Small datasets | ❌ Way too much wasted memory |
| Numeric data | ❌ Hash tables or BSTs are more appropriate |

---

## 9. 📌 Summary

| Concept | Detail |
|---------|--------|
| **Trie** | Short for "retrieval" — a tree made of arrays |
| **Each node** | An array of 26 pointers (one per letter) + an `is_word` flag |
| **How words are stored** | Implicitly, along the path — not as explicit strings |
| **Shared prefixes** | Words with common beginnings share nodes (TOAD and TOM share T→O) |
| **Search time** | O(1) — always equal to the length of the word, regardless of trie size |
| **Why O(1)?** | Words have a bounded maximum length (constant number of steps) |
| **Memory cost** | Very high — 26 pointers per node, most of them NULL |
| **"TOAD" alone** | Needs 4 nodes × 26 pointers = 104 pointers |
| **Best use case** | Autocomplete, spell-checking, fast prefix lookups |
| **Worst use case** | Small datasets where memory matters |
| **vs Hash table** | Trie: O(1) guaranteed, massive memory. Hash table: O(1) average, much less memory |
