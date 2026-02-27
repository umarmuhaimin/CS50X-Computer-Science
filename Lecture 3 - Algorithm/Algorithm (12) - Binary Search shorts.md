⚡ Binary Search — Doug Lloyd's Short (Lecture 3 @ CS50)

Binary search is a dramatically faster search algorithm — but it comes with a hard prerequisite: the array must be sorted. If that condition is met, binary search can eliminate half the remaining elements with every single step, turning what would be an O(n) scan into an O(log n) operation. For large datasets, this is the difference between thousands of steps and tens of steps.

---

## 1. 🧠 What Is Binary Search?

Binary search is built on a single powerful idea: **divide and conquer**.

Instead of checking elements one by one from the left, binary search:
1. Looks at the **middle** element
2. Decides whether the target is in the **left half** or the **right half**
3. Discards the irrelevant half entirely
4. Repeats on the surviving half

Each step cuts the problem in half. An array of 1,000 elements becomes 500, then 250, then 125, then 63 — reaching a single element after just 10 steps.

---

## 2. 🔒 The Required Condition — Sorted Array

Binary search works because of a guarantee only a **sorted array** can provide:

> Everything to the **left** of the current position is **smaller**.
> Everything to the **right** is **larger**.

Without this guarantee, you cannot safely discard half the array. If the array is unsorted, the element you're looking for could be anywhere — even on the side you just discarded. Sorting makes elimination safe.

- **Clarification — what if the array is unsorted?** You would first need to sort it using any sorting algorithm (selection sort, bubble sort, merge sort). The cost of that sort is a one-time investment — once sorted, every subsequent binary search is O(log n).

---

## 3. 📝 Pseudocode

```
Repeat until the sub-array has size 0:
    Calculate the midpoint of the current sub-array
    If the target is at the midpoint
        Stop (success — found it)
    Else if the target is less than the midpoint value
        Set the end to just left of the midpoint
        Repeat on the left half
    Else if the target is greater than the midpoint value
        Set the start to just right of the midpoint
        Repeat on the right half
If sub-array size reaches 0
    Stop (not found)
```

### Breaking it down

| Line | Meaning |
|------|---------|
| `Repeat until size 0` | Keep going until there are no elements left to search |
| `Calculate midpoint` | `mid = (start + end) / 2` — the middle index |
| `Target == midpoint value` | Found it! Stop immediately. |
| `Target < midpoint value` | Target must be in the **left half** — shrink end to `mid - 1` |
| `Target > midpoint value` | Target must be in the **right half** — advance start to `mid + 1` |
| `Size reaches 0` | `start > end` — the two ends have crossed; target is absent |

- **Clarification — why `mid - 1` and `mid + 1`, not just `mid`?** You already know the midpoint is not the target (you just checked). So you can safely exclude it by moving one step past it.

---

## 4. 🗂️ The Array Setup — 15 Elements

For both examples below, we use this sorted 15-element array:

```
Index:  [0]  [1]  [2]  [3]  [4]  [5]  [6]  [7]  [8]  [9]  [10] [11] [12] [13] [14]
Value:   1    3    5    7    9    11   13   15   17   19   21   23   25   27   29
```

Three variables are tracked throughout:
- **start** — left boundary of the current search area
- **end** — right boundary of the current search area
- **mid** — the midpoint, calculated fresh each iteration as `(start + end) / 2`

---

## 5. 🔁 Step-by-Step Example 1 — Target Found (searching for **19**)

### Initial state: `start = 0`, `end = 14`

---

**Iteration 1:**
```
start = 0, end = 14
mid = (0 + 14) / 2 = 7
value[7] = 15

Is 15 == 19? No.
Is 19 > 15? Yes → target is in the RIGHT half
New start = mid + 1 = 8
```

We've eliminated indices 0–7 (8 elements) in one step.

---

**Iteration 2:**
```
start = 8, end = 14
mid = (8 + 14) / 2 = 11
value[11] = 23

Is 23 == 19? No.
Is 19 < 23? Yes → target is in the LEFT half
New end = mid - 1 = 10
```

We've now eliminated indices 11–14 as well. Only indices 8–10 remain.

---

**Iteration 3:**
```
start = 8, end = 10
mid = (8 + 10) / 2 = 9
value[9] = 19

Is 19 == 19? YES! ✅ → STOP
```

**Result: Found at index 9 after just 3 comparisons!** ✅

### Summary table — finding 19

| Iteration | start | end | mid | value[mid] | Decision | New boundary |
|-----------|-------|-----|-----|------------|----------|-------------|
| 1 | 0 | 14 | 7 | 15 | 19 > 15 → go right | start = 8 |
| 2 | 8 | 14 | 11 | 23 | 19 < 23 → go left | end = 10 |
| 3 | 8 | 10 | **9** | **19** | **19 == 19 → FOUND** | — |

Without binary search, a linear scan would have taken 10 comparisons (checking indices 0 through 9). Binary search found it in 3.

---

## 6. 🔁 Step-by-Step Example 2 — Target Not Found (searching for **16**)

Note: 16 is not in the array (the array contains 15 at index 7 and 17 at index 8 — 16 sits in the gap between them). Binary search must work this out systematically.

### Initial state: `start = 0`, `end = 14`

---

**Iteration 1:**
```
start = 0, end = 14
mid = 7, value[7] = 15

Is 16 == 15? No.
Is 16 > 15? Yes → go right
New start = 8
```

---

**Iteration 2:**
```
start = 8, end = 14
mid = 11, value[11] = 23

Is 16 == 23? No.
Is 16 < 23? Yes → go left
New end = 10
```

---

**Iteration 3:**
```
start = 8, end = 10
mid = 9, value[9] = 19

Is 16 == 19? No.
Is 16 < 19? Yes → go left
New end = 8
```

---

**Iteration 4:**
```
start = 8, end = 8
mid = 8, value[8] = 17

Is 16 == 17? No.
Is 16 < 17? Yes → go left
New end = 7
```

---

**Crossed pointers check:**
```
start = 8, end = 7

start > end → sub-array has size 0 → STOP
```

**Result: Not found after 4 comparisons.** ✅

### Summary table — searching for 16

| Iteration | start | end | mid | value[mid] | Decision | New boundary |
|-----------|-------|-----|-----|------------|----------|-------------|
| 1 | 0 | 14 | 7 | 15 | 16 > 15 → go right | start = 8 |
| 2 | 8 | 14 | 11 | 23 | 16 < 23 → go left | end = 10 |
| 3 | 8 | 10 | 9 | 19 | 16 < 19 → go left | end = 8 |
| 4 | 8 | 8 | 8 | 17 | 16 < 17 → go left | end = 7 |
| — | **8 > 7** | — | — | — | **start > end → NOT FOUND** | — |

---

## 7. 🚦 Why `start > end` Means "Not Found"

This is a subtle but important concept. There are three states to distinguish:

| State | Meaning |
|-------|---------|
| `start < end` | Multiple elements to search — continue |
| `start == end` | One element remaining — check it |
| `start > end` | The two boundaries have **crossed** — sub-array is empty |

When `start > end`, there is no longer a valid sub-array to search. You've exhausted every possible location without finding the target. This is the definitive signal that the element is absent.

> **Important:** `start == end` is NOT "not found" yet — you must still check that one element. It's only when they **cross** (start > end) that you can guarantee absence.

In the example above: after iteration 4, `start = 8` and `end = 7`. If we had been looking for 17 (which is at index 8), it would have been found in iteration 4 before the cross happened. It's only because 16 isn't there at all that the boundaries crossed.

---

## 8. ⏱️ Running Time Analysis

### Worst case — O(log n)

In the worst case, you must keep halving the array until you either find the target or reach a sub-array of size 0. Starting from `n` elements:

```
Step 1: n elements → n/2
Step 2: n/2 → n/4
Step 3: n/4 → n/8
...
Step k: reach 1 element
```

The number of halvings needed to reach 1 from n is **log₂ n**. So the worst case is O(log n) — dramatically better than linear search's O(n).

```
Worst case: O(log n)
```

### Best case — Ω(1)

The best case happens when the target is the **midpoint of the first iteration** — you find it immediately.

For our 15-element array, if you were searching for 15 (at index 7), it would be found on the very first check.

```
Best case: Ω(1)
```

### Why there's no tight bound Θ

Best case (Ω(1)) and worst case (O(log n)) are different, so there is no single tight bound.

| Case | Scenario | Steps | Notation |
|------|----------|-------|---------|
| **Worst** | Target is absent or in last division | log₂ n | O(log n) |
| **Best** | Target is at the initial midpoint | 1 | Ω(1) |
| **Tight bound** | Best ≠ worst | — | No Θ |

### Concrete step count comparison

| Array size n | Linear search worst (n) | Binary search worst (log₂ n) |
|--------------|------------------------|------------------------------|
| 15 | 15 | 4 |
| 100 | 100 | 7 |
| 1,000 | 1,000 | 10 |
| 1,000,000 | 1,000,000 | 20 |
| 1,000,000,000 | 1,000,000,000 | 30 |

At n = 1 billion: linear search needs a billion comparisons. Binary search needs just 30.

---

## 9. 📌 Summary

### The algorithm

```
Repeat until sub-array size = 0:
    mid = (start + end) / 2
    If value[mid] == target → STOP (found)
    If target < value[mid] → end = mid - 1 (go left)
    If target > value[mid] → start = mid + 1 (go right)
If start > end → STOP (not found)
```

### Key concepts

| Concept | Detail |
|---------|--------|
| Strategy | Divide and conquer — eliminate half the array each step |
| Prerequisite | Array must be sorted |
| Midpoint formula | `mid = (start + end) / 2` |
| "Not found" signal | `start > end` — the two boundaries have crossed |
| Elements eliminated per step | Half the current sub-array |
| Levels needed | log₂ n halvings to reach a single element |

### Running time

| | Notation | Why |
|-|----------|-----|
| Worst case | O(log n) | Must halve log n times |
| Best case | Ω(1) | Target at initial midpoint |
| Tight bound | None | Best ≠ worst |

### Binary search vs. linear search

| | Linear Search | Binary Search |
|-|--------------|--------------|
| Requirement | None | Sorted array |
| Worst case | O(n) | **O(log n)** |
| Best case | Ω(1) | Ω(1) |
| Eliminates per step | 1 element | Half remaining |
| Use when | Unsorted or small arrays | Large sorted datasets |
