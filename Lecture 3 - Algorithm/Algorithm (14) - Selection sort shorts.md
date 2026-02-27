🔃 Selection Sort — Doug Lloyd's Short (Lecture 3 @ CS50)

Selection sort is an algorithm that builds a sorted array one element at a time by repeatedly finding the smallest unsorted element and moving it to the front. The name comes from what it does: it *selects* the minimum from the remaining unsorted data each pass. Simple and easy to understand, but it pays a significant price — even on an already-sorted array, it must scan everything from start to finish every single time.

---

## 1. 🧠 The Core Idea

Find the smallest unsorted element and add it to the end of the sorted list.

```
Pass 1: scan all n elements → smallest → position 0     ← sorted
Pass 2: scan n-1 elements → smallest → position 1       ← sorted
Pass 3: scan n-2 elements → smallest → position 2       ← sorted
...
Pass n: last element remains → position n-1             ← sorted
```

Each pass expands the sorted region by exactly one element from the **left**. The unsorted region shrinks from the left. This builds the sorted array **left to right, smallest to largest**.

---

## 2. 📝 Pseudocode

```
Repeat until no unsorted elements remain:
    Search through the unsorted data to find the smallest value
    Swap the smallest value with the first element of the unsorted part
```

### Breaking it down

| Line | Meaning |
|------|---------|
| `Repeat until no unsorted elements remain` | Outer loop — runs n times (once per element) |
| `Search through unsorted data` | Scan from position `i` to `n-1` — every element in the unsorted region |
| `Find the smallest value` | Track which element is smallest as you scan |
| `Swap with first element of unsorted part` | Place the minimum at the front of the unsorted region — now it's in its permanent sorted position |

- **Clarification — why scan from `i`, not always from `0`?** Everything to the left of `i` is already sorted and never needs revisiting. The scan starts at `i` and goes to the end.
- **Clarification — what if the smallest is already at the front?** You still "swap" it with itself — a no-op that changes nothing. The algorithm doesn't detect this as a special case; it just happens to result in no change.

---

## 3. 🔁 Step-by-Step Example

### Array: `{5, 2, 1, 3, 4, 6}` (n = 6)

All elements start as unsorted (🔴). As each element is placed in its final position, it becomes sorted (🔵).

---

**Pass 1 — i = 0, scan indices 0 to 5:**
```
Unsorted region: {5, 2, 1, 3, 4, 6}
Smallest found:  1 (at index 2)
First of unsorted: 5 (at index 0)
Swap index 0 ↔ index 2: 5 ↔ 1
```
Before: 🔴{5, 2, 1, 3, 4, 6}
After:  🔵{1,} 🔴{2, 5, 3, 4, 6}

1 is now sorted at position 0. ✅

---

**Pass 2 — i = 1, scan indices 1 to 5:**
```
Unsorted region: {2, 5, 3, 4, 6}
Smallest found:  2 (at index 1)
First of unsorted: 2 (at index 1)
Swap index 1 ↔ index 1: 2 ↔ 2 (swap with itself — no change)
```
Before: 🔵{1,} 🔴{2, 5, 3, 4, 6}
After:  🔵{1, 2,} 🔴{5, 3, 4, 6}

2 is confirmed sorted at position 1. ✅

---

**Pass 3 — i = 2, scan indices 2 to 5:**
```
Unsorted region: {5, 3, 4, 6}
Smallest found:  3 (at index 3)
First of unsorted: 5 (at index 2)
Swap index 2 ↔ index 3: 5 ↔ 3
```
Before: 🔵{1, 2,} 🔴{5, 3, 4, 6}
After:  🔵{1, 2, 3,} 🔴{5, 4, 6}

3 is now sorted at position 2. ✅

---

**Pass 4 — i = 3, scan indices 3 to 5:**
```
Unsorted region: {5, 4, 6}
Smallest found:  4 (at index 4)
First of unsorted: 5 (at index 3)
Swap index 3 ↔ index 4: 5 ↔ 4
```
Before: 🔵{1, 2, 3,} 🔴{5, 4, 6}
After:  🔵{1, 2, 3, 4,} 🔴{5, 6}

4 is now sorted at position 3. ✅

---

**Pass 5 — i = 4, scan indices 4 to 5:**
```
Unsorted region: {5, 6}
Smallest found:  5 (at index 4)
First of unsorted: 5 (at index 4)
Swap index 4 ↔ index 4: 5 ↔ 5 (swap with itself — no change)
```
Before: 🔵{1, 2, 3, 4,} 🔴{5, 6}
After:  🔵{1, 2, 3, 4, 5,} 🔴{6}

5 is confirmed sorted at position 4. ✅

---

**Pass 6 — i = 5, only one element remains:**
```
Unsorted region: {6}
Single element — already in its correct position by elimination
```
After:  🔵{1, 2, 3, 4, 5, 6}

6 is sorted at position 5. ✅

---

**Final result: `{1, 2, 3, 4, 5, 6}`** ✅

### All passes at a glance

| Pass | i | Unsorted region | Min found | Swap | Array after |
|------|---|----------------|-----------|------|-------------|
| 1 | 0 | {5,2,1,3,4,6} | 1 at idx 2 | idx 0 ↔ idx 2 | **1**, 2, 5, 3, 4, 6 |
| 2 | 1 | {2,5,3,4,6} | 2 at idx 1 | idx 1 ↔ idx 1 | 1, **2**, 5, 3, 4, 6 |
| 3 | 2 | {5,3,4,6} | 3 at idx 3 | idx 2 ↔ idx 3 | 1, 2, **3**, 5, 4, 6 |
| 4 | 3 | {5,4,6} | 4 at idx 4 | idx 3 ↔ idx 4 | 1, 2, 3, **4**, 5, 6 |
| 5 | 4 | {5,6} | 5 at idx 4 | idx 4 ↔ idx 4 | 1, 2, 3, 4, **5**, 6 |
| 6 | 5 | {6} | 6 (only one) | — | 1, 2, 3, 4, 5, **6** |

Bold = element placed into sorted position that pass.

---

## 4. ⏱️ Running Time Analysis

### Worst case — O(n²)

In the absolute worst case (reverse-sorted array), every pass must scan the entire remaining unsorted region:

- Pass 1: scan n elements → n - 1 comparisons
- Pass 2: scan n-1 elements → n - 2 comparisons
- Pass 3: scan n-2 elements → n - 3 comparisons
- ...
- Pass n-1: scan 2 elements → 1 comparison

Total:
```
(n - 1) + (n - 2) + (n - 3) + ... + 1 = n(n - 1) / 2
```

Simplify to Big O:
```
n(n-1)/2 = (n² - n)/2 = n²/2 - n/2
→ drop constants and lower-order term
= O(n²)
```

### Best case — also Ω(n²)

Here is the critical weakness of selection sort:

> **Even if the array is already perfectly sorted, selection sort still does n(n-1)/2 comparisons.**

Why? Because to find the minimum of the unsorted region, it **must scan every element** in that region. It has no way to know "this is already the smallest without checking." There is no early-exit mechanism.

```
Best case: Ω(n²)     ← same as worst case
```

### Tight bound — Θ(n²)

Since the best case and worst case are both n(n-1)/2, selection sort is Θ(n²): always quadratic, regardless of the initial ordering.

```
Θ(n²) — always exactly n(n-1)/2 comparisons, every single time.
```

| Case | Scenario | Comparisons | Notation |
|------|----------|-------------|---------|
| **Worst** | Reverse sorted | n(n-1)/2 | O(n²) |
| **Best** | Already sorted | n(n-1)/2 | Ω(n²) |
| **Tight bound** | Best = worst | n(n-1)/2 | **Θ(n²)** |

---

## 5. ⚖️ Comparing Selection Sort and Bubble Sort

| | Selection Sort | Bubble Sort |
|-|---------------|-------------|
| Worst case | O(n²) | O(n²) |
| **Best case** | **Ω(n²)** | **Ω(n)** ← better |
| Tight bound | **Θ(n²)** | None |
| Can detect sorted array? | ❌ No | ✅ Yes (swap counter) |
| Sorted region grows from | Left (smallest first) | Right (largest first) |

Selection sort's Θ(n²) is actually a *worse* property than bubble sort's lack of Θ: bubble sort at least has a chance of finishing early. Selection sort never does.

---

## 6. 📌 Summary

### The algorithm

```
For i from 0 to n-1:
    Find the minimum in numbers[i] to numbers[n-1]
    Swap the minimum with numbers[i]
```

### Key mechanics

| Concept | Detail |
|---------|--------|
| Strategy | Find the minimum in the unsorted region, move it to the front |
| Sorted region | Grows from the **left** — positions 0 to i-1 are always sorted |
| Unsorted region | Shrinks from the left — positions i to n-1 remain |
| Scan range | Always from `i` to `n-1`, never revisiting sorted elements |
| Swap-with-self | If minimum is already at position `i`, the swap changes nothing |

### Running time

| | Notation | Comparisons | Why |
|-|----------|-------------|-----|
| Worst case | O(n²) | n(n-1)/2 | Must scan all of unsorted region each pass |
| Best case | Ω(n²) | n(n-1)/2 | No early exit — scans everything regardless |
| Tight bound | **Θ(n²)** | n(n-1)/2 | Best = worst — always the same amount of work |

### Deriving O(n²)

```
Steps = (n-1) + (n-2) + ... + 1
      = n(n-1)/2
      = (n² - n)/2
      → drop constants and lower-order term
      = O(n²)
```

Selection sort is the textbook example of an algorithm with no adaptivity — it does exactly the same amount of work whether the input is already sorted or completely scrambled. Understanding this limitation is what motivates learning faster algorithms like merge sort.
