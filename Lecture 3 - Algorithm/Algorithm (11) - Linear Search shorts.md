🔍 Linear Search — Doug Lloyd's Short (Lecture 3 @ CS50)

Linear search is the simplest possible search algorithm: check every element one by one, from left to right, until you find the target — or run out of elements. It makes no assumptions about the data, requires no prior sorting, and works on any collection. As Doug Lloyd puts it: "iterate across the array from left to right, looking for a specified element."

---

## 1. 🧠 What Is Linear Search?

An **algorithm** is a step-by-step set of instructions for completing a task. Linear search is one such algorithm — specifically designed for finding an element inside an array.

**Core idea:** Start at the leftmost element and check each one in sequence. The moment you find the target, stop. If you reach the end without a match, the target is not in the array.

There's no clever trick here — just methodical checking. That simplicity is both its strength (no setup required) and its limitation (can be slow for large arrays).

---

## 2. 📝 Pseudocode

```
If the first element is what you're looking for
    Stop (success — found it)
Otherwise
    Move to the next element and repeat
If no elements remain
    Stop (not found)
```

### Breaking it down

| Step | Meaning |
|------|---------|
| Start at element 0 | Always begin at the leftmost position |
| Check current element | Is this equal to the target? |
| Match → stop | You found it. Done. |
| No match → advance | Move one step to the right |
| No elements left | You've checked all of them — target is not in the array |

- **Clarification — what does "stop" mean when not found?** The algorithm doesn't fail — it successfully concludes that the target is absent. It checked every element and can now guarantee the answer.

---

## 3. 🔁 Step-by-Step Example — Target Found

### Array: `{11, 23, 2, 8, 9, 7, 4}` — searching for **9**

| Step | Index | Value | Match? | Action |
|------|-------|-------|--------|--------|
| 1 | 0 | 11 | ❌ No | Move to next |
| 2 | 1 | 23 | ❌ No | Move to next |
| 3 | 2 | 2 | ❌ No | Move to next |
| 4 | 3 | 8 | ❌ No | Move to next |
| 5 | 4 | **9** | ✅ **Yes!** | **Stop — found at index 4** |

**Result: Found after 5 comparisons.** ✅

The algorithm stops immediately upon finding the match — it does not continue checking indices 5 and 6. This is the **early exit** behaviour: linear search only looks at what it needs to.

---

## 4. 🔁 Step-by-Step Example — Target Not Found

### Same array: `{11, 23, 2, 8, 9, 7, 4}` — searching for **50**

| Step | Index | Value | Match? | Action |
|------|-------|-------|--------|--------|
| 1 | 0 | 11 | ❌ No | Move to next |
| 2 | 1 | 23 | ❌ No | Move to next |
| 3 | 2 | 2 | ❌ No | Move to next |
| 4 | 3 | 8 | ❌ No | Move to next |
| 5 | 4 | 9 | ❌ No | Move to next |
| 6 | 5 | 7 | ❌ No | Move to next |
| 7 | 6 | 4 | ❌ No | Move to next |
| 8 | — | (end) | No elements left | **Stop — not found** |

**Result: Not found after 7 comparisons (every element checked).** ✅

> **Key insight:** The algorithm "succeeds" even when the target isn't there. To confirm absence, you must check every single element — you can't declare "not found" early because the target might always be at the next position. Only exhausting the array gives a definitive answer.

---

## 5. ⏱️ Running Time Analysis

### Worst case — O(n)

The worst case happens when:
- The target is the **last element** in the array, OR
- The target **does not exist** in the array at all

In both cases, every single element must be checked before giving an answer. For an array of `n` elements → `n` comparisons.

```
Worst case: O(n)
```

### Best case — Ω(1)

The best case happens when:
- The target is the **very first element** (index 0)

You check one element, find a match immediately, and stop. Only 1 comparison regardless of how large the array is.

```
Best case: Ω(1)
```

### Why there's no tight bound Θ

Since the best case (Ω(1)) and worst case (O(n)) are different, there is **no single tight bound** Θ for linear search. The running time genuinely varies depending on where the target is.

| Case | Scenario | Comparisons | Notation |
|------|----------|-------------|---------|
| **Worst** | Target is last, or absent | n | O(n) |
| **Best** | Target is at index 0 | 1 | Ω(1) |
| **Tight bound** | Best ≠ worst | — | No Θ |

---

## 6. ✅ Key Properties of Linear Search

| Property | Detail |
|----------|--------|
| **Requires sorted array?** | ❌ No — works on any ordering |
| **Works on any data?** | ✅ Yes — numbers, strings, any type |
| **Guaranteed to find target?** | ✅ Yes — if it's in the array |
| **Guaranteed to report not-found?** | ✅ Yes — by exhaustive checking |
| **Stops early if found?** | ✅ Yes — exits immediately on match |
| **Memory usage** | O(1) — no extra memory needed |

---

## 7. 📌 Summary

### The algorithm

```
For each element in the array (left to right):
    If element == target → stop (found)
Otherwise (exhausted array) → stop (not found)
```

### Running time

| | Notation | Why |
|-|----------|-----|
| Worst case | O(n) | Must check every element |
| Best case | Ω(1) | Target found on first check |
| Tight bound | None | Best ≠ worst |

### Where linear search fits

| Algorithm | Requirement | Worst case |
|-----------|-------------|------------|
| Linear search | None | O(n) |
| Binary search | Sorted array | O(log n) |

Linear search is slower than binary search in the worst case, but it works on **any array** without needing prior sorting. For small arrays or one-off searches on unsorted data, linear search is perfectly reasonable — sometimes the overhead of sorting just to then binary-search isn't worth it.
