🔍 Binary Search (Lecture 3 — Algorithms @ CS50)

Binary search is a dramatically faster alternative to linear search — but it comes with one important requirement: the array **must already be sorted**. Instead of checking every element one by one, binary search repeatedly **divides the problem in half**, zeroing in on the target with each step. This is the algorithm behind the green **log₂ n** curve from the efficiency graph.

---

## 1. 🧠 The Core Idea — Divide and Conquer

Linear search is like reading a dictionary page by page from A to Z. Binary search is like opening the dictionary to the **middle**, checking if your word is before or after that page, and then only searching the relevant half. Then you repeat — split that half in half again — until you land on your word or run out of pages.

### Why is this so powerful?

Each step **eliminates half** of the remaining elements. This means the number of steps grows logarithmically:

| Array size (n) | Max steps for linear search | Max steps for binary search (log₂ n) |
|----------------|----------------------------|---------------------------------------|
| 7 | 7 | 3 |
| 100 | 100 | 7 |
| 1,000 | 1,000 | 10 |
| 1,000,000 | 1,000,000 | 20 |
| 4,000,000,000 | 4 billion | 32 |

- **Clarification — log₂ n explained simply:** "How many times can you cut `n` in half before you reach 1?" For 8 items: 8 → 4 → 2 → 1 = 3 cuts, so log₂ 8 = 3. For 1,000,000 items: only about 20 cuts. This is why binary search scales so well.

### The one requirement ⚠️

> **The array MUST be sorted** (smallest to largest, or some defined order).

Binary search relies on comparing the target to the middle element and deciding which half to search. If the array isn't sorted, "left half" and "right half" have no meaning — the algorithm will give wrong answers.

---

## 2. 📝 Pseudocode — Human-Readable Version

Here is the logic of binary search expressed in plain English:

```
If no doors left
    Return false
If 50 is behind middle door
    Return true
Else if 50 < middle door
    Search left half
Else if 50 > middle door
    Search right half
```

### Breaking this down line by line

| Line | What it does | Why it's needed |
|------|-------------|-----------------|
| `If no doors left` | **Base case** — we've run out of elements to check | Prevents infinite searching; if we've narrowed down to nothing, the target simply isn't here |
| `Return false` | Target not found | Only reached when every possibility has been eliminated |
| `If 50 is behind middle door` | Check the middle element | This is the key move — go straight to the center |
| `Return true` | Found it! | Stop immediately — no need to continue |
| `Else if 50 < middle door` | Target is smaller than the middle value | Everything to the **right** of middle is even bigger → eliminate the right half |
| `Search left half` | Repeat the same process on the left portion only | Problem just got cut in half |
| `Else if 50 > middle door` | Target is bigger than the middle value | Everything to the **left** of middle is even smaller → eliminate the left half |
| `Search right half` | Repeat the same process on the right portion only | Problem just got cut in half again |

- **Clarification — "middle door":** For an array of size 7 (indices 0–6), the middle is index 3. For an even-sized array (size 8, indices 0–7), the middle is typically index 3 (using integer division: `(0 + 7) / 2 = 3`). The choice of rounding down vs. up doesn't affect correctness.

---

## 3. 💻 Pseudocode — Closer to Code

Using array notation and index variables, the same algorithm looks like this:

```
If no doors left
    Return false
If 50 is behind doors[middle]
    Return true
Else if 50 < doors[middle]
    Search doors[0] through doors[middle - 1]
Else if 50 > doors[middle]
    Search doors[middle + 1] through doors[n - 1]
```

### Breaking this down

| Line | Translation | What's happening |
|------|------------|-----------------|
| `doors[middle]` | The element at the middle index of the current search range | This is what we compare against |
| `doors[0] through doors[middle - 1]` | The **left half** — everything before the middle | We exclude `doors[middle]` itself (already checked) |
| `doors[middle + 1] through doors[n - 1]` | The **right half** — everything after the middle | Again, middle is excluded |

- **Clarification — how `middle` is calculated:** `middle = (low + high) / 2`, where `low` is the start of the current search range and `high` is the end. In C, integer division automatically truncates (rounds down).
- **Clarification — "no doors left":** This happens when `low > high` — the search range has shrunk to nothing. The start index has passed the end index, meaning there are zero elements left to check.

Notice that this pseudocode is nearly ready to be translated into real C code. The structure maps directly to `if`/`else if` conditions and array indexing.

---

## 4. 🔎 Full Step-by-Step Example

### Searching for `50` in the array `{4, 8, 15, 16, 23, 42, 50}`

```
Index:   0    1    2    3    4    5    6
Value:  [4]  [8]  [15] [16] [23] [42] [50]
```

**Step 1:** Search range = entire array (index 0 to 6)
```
middle = (0 + 6) / 2 = 3
doors[3] = 16
Is 50 == 16?  No.
Is 50 < 16?   No.
Is 50 > 16?   Yes → search RIGHT half (indices 4 to 6)
```
Eliminated: indices 0, 1, 2, 3 (4 elements gone in one step!)

**Step 2:** Search range = indices 4 to 6
```
                              [23] [42] [50]
                               4    5    6

middle = (4 + 6) / 2 = 5
doors[5] = 42
Is 50 == 42?  No.
Is 50 < 42?   No.
Is 50 > 42?   Yes → search RIGHT half (indices 6 to 6)
```
Eliminated: indices 4, 5

**Step 3:** Search range = index 6 to 6 (one element left)
```
                                        [50]
                                         6

middle = (6 + 6) / 2 = 6
doors[6] = 50
Is 50 == 50?  YES → Return true ✅
```

**Result:** Found `50` in **3 steps** (vs. 7 steps with linear search).

### Summary table for this search

| Step | Search range | `middle` index | `doors[middle]` | Comparison | Action |
|------|-------------|----------------|-----------------|------------|--------|
| 1 | 0–6 (7 elements) | 3 | 16 | 50 > 16 | Search right half (4–6) |
| 2 | 4–6 (3 elements) | 5 | 42 | 50 > 42 | Search right half (6–6) |
| 3 | 6–6 (1 element) | 6 | 50 | 50 == 50 | **Return true** ✅ |

---

## 5. ❌ Step-by-Step Example — Target Not Found

### Searching for `99` in the array `{4, 8, 15, 16, 23, 42, 50}`

**Step 1:** Search range = 0 to 6
```
middle = 3 → doors[3] = 16
99 > 16 → search RIGHT half (4–6)
```

**Step 2:** Search range = 4 to 6
```
middle = 5 → doors[5] = 42
99 > 42 → search RIGHT half (6–6)
```

**Step 3:** Search range = 6 to 6
```
middle = 6 → doors[6] = 50
99 > 50 → search RIGHT half (7–6)
```

**Step 4:** Search range = 7 to 6
```
low (7) > high (6) → NO DOORS LEFT → Return false ❌
```

| Step | Search range | `middle` | `doors[middle]` | Action |
|------|-------------|----------|-----------------|--------|
| 1 | 0–6 | 3 | 16 | 99 > 16 → go right (4–6) |
| 2 | 4–6 | 5 | 42 | 99 > 42 → go right (6–6) |
| 3 | 6–6 | 6 | 50 | 99 > 50 → go right (7–6) |
| 4 | 7–6 | — | — | **No doors left → Return false** ❌ |

**Result:** `99` not found in **4 steps** (vs. 7 steps with linear search). Even the failure case is faster.

---

## 6. ⚖️ Linear Search vs. Binary Search — Side by Side

| Aspect | Linear Search | Binary Search |
|--------|--------------|---------------|
| **Strategy** | Check every element, left to right | Jump to middle, eliminate half each step |
| **Requires sorted array?** | ❌ No — works on any array | ✅ Yes — must be sorted |
| **Best case** | 1 step (target is first) | 1 step (target is in the middle) |
| **Worst case** | n steps | log₂ n steps |
| **Growth rate** | O(n) — linear 🔴 | O(log n) — logarithmic 🟢 |
| **7 elements worst case** | 7 checks | 3 checks |
| **1,000,000 elements worst case** | 1,000,000 checks | 20 checks |
| **Simplicity** | Very simple to implement | Slightly more complex (track low/high) |

### When to use which?

| Situation | Best choice | Why |
|-----------|-------------|-----|
| Small array (< 20 elements) | Either works | Difference is negligible |
| Unsorted array | Linear search | Binary search won't work without sorting first |
| Large sorted array | Binary search | Massive speed advantage |
| Searching once in unsorted data | Linear search | Sorting first + binary search may cost more than one linear scan |
| Searching many times in same data | Sort once + binary search each time | Sorting cost is paid once; every subsequent search is log₂ n |

---

## 7. 🔑 The Recursive Nature of Binary Search

Notice something special about the pseudocode: after comparing with the middle, binary search tells you to **"search the left half"** or **"search the right half"** — which is the exact same problem, just smaller. This is called **recursion** — a function that calls itself with a smaller input.

```
Binary search on full array
  → Binary search on half the array
    → Binary search on quarter of the array
      → Binary search on one element
        → Found it, or no doors left
```

Each call does the same thing:
1. Check if there's anything left to search
2. Look at the middle
3. Decide which half to search next

This "self-similar" structure is what makes binary search elegant. You'll explore recursion formally later in this lecture.

---

## 8. 📌 Summary

### Binary search algorithm
```
If no doors left → Return false
If target == doors[middle] → Return true
If target < doors[middle] → Search left half
If target > doors[middle] → Search right half
```

### Key facts
| Fact | Detail |
|------|--------|
| Requirement | Array **must be sorted** |
| Strategy | Compare with middle, eliminate half |
| Best case | 1 step (target is the middle element) |
| Worst case | log₂ n steps |
| How `middle` is found | `(low + high) / 2` (integer division) |
| "No doors left" means | `low > high` — search range is empty |
| Growth curve | 🟢 Logarithmic — the flattest line on the graph |
| Recursive? | Yes — each step is the same algorithm on a smaller array |

### The power of halving — why log₂ n is incredible
| Times you halve | Elements remaining (from 1,024) |
|-----------------|-------------------------------|
| 0 | 1,024 |
| 1 | 512 |
| 2 | 256 |
| 3 | 128 |
| 4 | 64 |
| 5 | 32 |
| 6 | 16 |
| 7 | 8 |
| 8 | 4 |
| 9 | 2 |
| 10 | 1 ← done |

10 steps to search 1,024 items. 20 steps for 1,048,576 items. 30 steps for over a billion. That's the magic of **divide and conquer**.
