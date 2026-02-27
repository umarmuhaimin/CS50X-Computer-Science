🔃 Sorting & Selection Sort (Lecture 3 — Algorithms @ CS50)

So far we've been searching arrays. But searching is only fast on a **sorted** array — binary search requires it. This raises the next question: how do we get an array sorted in the first place? There are many sorting algorithms, each with different trade-offs in speed and complexity. We start with one of the most intuitive: **selection sort**.

---

## 1. 🧩 Why Sorting Matters

| State of array | Available search algorithms | Best search speed |
|---------------|----------------------------|------------------|
| Unsorted | Linear search only | O(n) |
| Sorted | Linear search OR binary search | O(log n) |

Sorting unlocks binary search. If you have a dataset you'll search repeatedly, the cost of sorting it once is paid back many times over by the speed of every subsequent search.

- **Clarification — the trade-off:** Sorting takes time upfront. If you only need to search once, it may be faster to just linear-search the unsorted array than to sort it and then binary-search it. But for repeated searches on the same data, sort once and binary-search every time.

---

## 2. 📦 Representing an Array

**(Image 1)**

This shows an array of `n` elements in memory — red blocks side by side, indexed from left to right:

```
Index:  [0]  [1]  [2]  [...]  [n-3]  [n-2]  [n-1]
```

- `[0]` is the **first** element (leftmost)
- `[n-1]` is the **last** element (rightmost)
- The `[...]` represents all the elements in between

This is the array we want to sort. Right now, the values inside are in no particular order. Our goal: rearrange the values so they go from **smallest to largest**.

---

## 3. 💡 The Core Idea of Selection Sort

Selection sort works by repeatedly **selecting the smallest remaining element** and placing it in its correct position at the front.

**Human intuition version:**
1. Look through the entire unsorted portion — find the smallest value.
2. Swap it into the first position of the unsorted portion.
3. That position is now "locked in" — it's sorted.
4. Repeat on the remaining unsorted portion.

After each pass, the sorted region grows by one element from the left, and the unsorted region shrinks by one from the left.

```
Pass 1: scan all n elements → smallest goes to position 0
Pass 2: scan remaining n-1 elements → smallest goes to position 1
Pass 3: scan remaining n-2 elements → smallest goes to position 2
...
Pass n-1: scan last 2 elements → smallest goes to position n-2
Done: all n elements sorted
```

---

## 4. 📝 Pseudocode

```
For i from 0 to n–1
    Find smallest number between numbers[i] and numbers[n-1]
    Swap smallest number with numbers[i]
```

### Breaking it down

| Line | Meaning |
|------|---------|
| `For i from 0 to n-1` | Outer loop — `i` marks the start of the **unsorted** region. It starts at 0 and advances right each pass. |
| `Find smallest number between numbers[i] and numbers[n-1]` | Scan everything from position `i` to the end — the unsorted region. |
| `Swap smallest number with numbers[i]` | Put the smallest element at the front of the unsorted region — now it belongs to the sorted region. |

- **Clarification — why start the inner scan at `i`, not always at `0`?** Everything to the **left** of `i` is already sorted. We never need to look at it again. We only scan from `i` onward — the unsorted portion.
- **Clarification — the swap:** A swap exchanges the values of two positions. If `numbers[i] = 8` and the smallest is `numbers[j] = 1`, after the swap: `numbers[i] = 1` and `numbers[j] = 8`. The smallest is now correctly at position `i`.

---

## 5. 🔁 Full Step-by-Step Example

### Array: `{5, 2, 7, 1, 3}`

```
Initial: [5]  [2]  [7]  [1]  [3]
Index:    0    1    2    3    4
```

**Pass 1 — i = 0, scan positions 0 to 4:**
```
Unsorted region: {5, 2, 7, 1, 3}
Smallest = 1 (at index 3)
Swap numbers[0] and numbers[3]: 5 ↔ 1

Before: [5]  [2]  [7]  [1]  [3]
After:  [1]  [2]  [7]  [5]  [3]
         ↑ sorted
```

**Pass 2 — i = 1, scan positions 1 to 4:**
```
Unsorted region: {2, 7, 5, 3}
Smallest = 2 (at index 1)
Swap numbers[1] and numbers[1]: 2 ↔ 2 (swap with itself — no change)

Before: [1]  [2]  [7]  [5]  [3]
After:  [1]  [2]  [7]  [5]  [3]
         ↑    ↑ sorted
```

**Pass 3 — i = 2, scan positions 2 to 4:**
```
Unsorted region: {7, 5, 3}
Smallest = 3 (at index 4)
Swap numbers[2] and numbers[4]: 7 ↔ 3

Before: [1]  [2]  [7]  [5]  [3]
After:  [1]  [2]  [3]  [5]  [7]
         ↑    ↑    ↑ sorted
```

**Pass 4 — i = 3, scan positions 3 to 4:**
```
Unsorted region: {5, 7}
Smallest = 5 (at index 3)
Swap numbers[3] and numbers[3]: 5 ↔ 5 (swap with itself — no change)

Before: [1]  [2]  [3]  [5]  [7]
After:  [1]  [2]  [3]  [5]  [7]
         ↑    ↑    ↑    ↑ sorted
```

**Pass 5 — i = 4, only one element left — already in place.**

**Final result: `[1]  [2]  [3]  [5]  [7]` ✅**

### Summary table

| Pass | `i` | Unsorted region | Smallest found | Swap | Array after pass |
|------|-----|----------------|----------------|------|-----------------|
| 1 | 0 | {5,2,7,1,3} | 1 at index 3 | pos 0 ↔ pos 3 | **1**, 2, 7, 5, 3 |
| 2 | 1 | {2,7,5,3} | 2 at index 1 | pos 1 ↔ pos 1 | 1, **2**, 7, 5, 3 |
| 3 | 2 | {7,5,3} | 3 at index 4 | pos 2 ↔ pos 4 | 1, 2, **3**, 5, 7 |
| 4 | 3 | {5,7} | 5 at index 3 | pos 3 ↔ pos 3 | 1, 2, 3, **5**, 7 |
| 5 | 4 | {7} | only one element | — | 1, 2, 3, 5, **7** |

Bold = newly placed in sorted position that pass.

---

## 6. ⏱️ Running Time Analysis

### Counting the steps

Each pass scans the unsorted region to find the minimum:
- Pass 1 (i=0): scan `n` elements → `n - 1` comparisons
- Pass 2 (i=1): scan `n-1` elements → `n - 2` comparisons
- Pass 3 (i=2): scan `n-2` elements → `n - 3` comparisons
- ...
- Pass n-1 (i=n-2): scan 2 elements → `1` comparison

Total comparisons:

```
(n - 1) + (n - 2) + (n - 3) + ... + 1
```

This is the **sum of integers from 1 to n-1**. There's a famous formula for this:

```
Sum = n(n - 1) / 2
```

### Worked example with n = 5:

```
(5-1) + (5-2) + (5-3) + (5-4) = 4 + 3 + 2 + 1 = 10
Formula check: 5 × (5-1) / 2 = 5 × 4 / 2 = 20 / 2 = 10 ✅
```

### Simplifying to Big O

```
n(n - 1) / 2  =  (n² - n) / 2  =  n²/2 - n/2
```

- Drop constants: `1/2` → gone
- Drop lower-order term: `n/2` → gone (dominated by `n²` for large n)

**Result: O(n²)**

---

## 7. 🔬 Worst Case vs. Best Case

Here is where selection sort reveals a critical weakness:

| Case | Scenario | Steps | Notation |
|------|----------|-------|---------|
| **Worst case** | Array is sorted in reverse order | n(n-1)/2 | O(n²) |
| **Best case** | Array is already sorted | n(n-1)/2 | Ω(n²) |

**The best case is also O(n²).** Why?

Because selection sort **always scans the entire unsorted region** to find the minimum — even if the array is already perfectly sorted. It has no way to detect "I'm already done." It must complete all passes regardless.

This means the upper bound and lower bound are the **same**: Θ(n²).

- **Clarification — compare to linear search:** Linear search has Ω(1) because it might find the target on the first check. Selection sort has no such luck — even in the best case, it still does n(n-1)/2 comparisons. It's always quadratic.
- **Clarification — why is this bad?** For n = 1,000: 1,000 × 999 / 2 ≈ **500,000 comparisons** just to sort. For n = 1,000,000: about **500 billion comparisons**. This is why better sorting algorithms (like merge sort at O(n log n)) are used in practice for large datasets.

---

## 8. 📌 Summary

### Selection sort — how it works

```
For i from 0 to n-1:
    Find smallest in numbers[i] to numbers[n-1]
    Swap smallest with numbers[i]
```

| Concept | Detail |
|---------|--------|
| Strategy | Repeatedly select the minimum from the unsorted region and place it next |
| Sorted region | Grows from left — positions 0 to i-1 are always sorted |
| Unsorted region | Shrinks from left — positions i to n-1 still to be processed |
| Swap | Exchanges the minimum with the element at position `i` |
| Passes required | n - 1 passes total |

### Running time

| | Notation | Value (steps) |
|-|----------|--------------|
| Worst case | O(n²) | n(n-1)/2 |
| Best case | Ω(n²) | n(n-1)/2 |
| Tight bound | **Θ(n²)** | Always the same — best = worst |

### Deriving O(n²)

```
Steps = (n-1) + (n-2) + ... + 1
      = n(n-1)/2
      = (n² - n)/2
      → drop constants and lower-order terms
      = O(n²)
```

### Where selection sort fits in the hierarchy

| Algorithm | Big O |
|-----------|-------|
| Binary search | O(log n) |
| Linear search | O(n) |
| **Selection sort** | **O(n²)** |
| Bubble sort | O(n²) |
| Merge sort | O(n log n) |

Selection sort is simple to understand and implement, but quadratic time makes it impractical for large datasets. It serves as a foundation to understand better algorithms like merge sort, which we'll cover later.
