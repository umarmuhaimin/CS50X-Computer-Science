📋 Introduction & Linear Search (Lecture 3 — Algorithms @ CS50)

In Week 0, we introduced the idea of an **algorithm**: a black box that takes an input and produces an output. This week, we expand that understanding — moving from pseudocode into real code, and critically, we start measuring how **efficient** algorithms are. Not all solutions are created equal: two algorithms can solve the same problem, but one might take seconds while the other takes hours.

---

## 1️⃣ Algorithm Efficiency — Why It Matters

### Consider the following graph:

**(Image 1)**

This graph plots **size of problem** (x-axis) against **time to solve** (y-axis) for three different growth rates:

| Line | Growth rate | What it means |
|------|------------|---------------|
| 🔴 Red | **n** (linear) | Time grows directly with problem size. Double the input → double the time. |
| 🟡 Yellow | **n/2** (linear, but halved) | Still linear growth, but roughly twice as fast as `n`. Double the input → still double the time, just starts lower. |
| 🟢 Green | **log₂ n** (logarithmic) | Time grows incredibly slowly. Double the input → only one extra step. |

### What does this mean in practice?

Imagine searching through a phone book with 1,000 pages:

| Algorithm type | Steps for 1,000 pages | Steps for 1,000,000 pages |
|----------------|----------------------|--------------------------|
| **n** (linear) | 1,000 | 1,000,000 |
| **n/2** (linear halved) | 500 | 500,000 |
| **log₂ n** (logarithmic) | ~10 | ~20 |

- **Clarification on n (linear):** This is the "check every single item" approach. If there are `n` items, you do `n` checks. It's the simplest and most straightforward, but the slowest for large inputs.
- **Clarification on n/2 (linear halved):** This is still fundamentally linear — it grows at the same rate, just with a constant factor of improvement. It's like flipping through a phone book two pages at a time instead of one. Faster, but not fundamentally different.
- **Clarification on log₂ n (logarithmic):** This is the "divide and conquer" approach — like binary search, which we'll see next. Each step cuts the problem in half. Going from 1,000 to 1,000,000 items (a 1000× increase) only adds about 10 extra steps. This is the power of logarithmic algorithms.

### The key insight
As the problem size grows, the **shape** of the curve matters far more than any constant speedup. `n/2` is faster than `n`, but they both shoot upward. `log₂ n` barely rises at all. This is why **algorithm design** matters — choosing a better algorithm can make the difference between a program that takes milliseconds and one that takes hours.

---

## 2️⃣ What Is an Array? (Quick Recall)

From last week (Lecture 2): an **array** is a block of memory locations that sit **consecutively** — side-by-side in memory.

### Visual representation

**(Image 2)**

This shows an array of 7 elements, visualized as red lockers:
```
[0]   [1]   [2]   [3]   [4]   [5]   [6]
```

| Position | Name | Description |
|----------|------|-------------|
| `[0]` | First element | The far-left locker — the **beginning** of the array |
| `[6]` | Last element | The far-right locker — the **end** of the array (for size 7) |

- **Clarification — zero-indexing:** Arrays start counting at `0`, not `1`. An array with 7 elements has indices `0` through `6`. The last index is always `size - 1`.
- **Clarification — "lockers" metaphor:** Think of each array position as a closed locker. You don't know what's inside until you open it (access that index). A search algorithm is essentially the strategy for deciding which lockers to open, and in what order.

---

## 3️⃣ The Search Problem

### The question
> "Is the number 50 inside this array?"

A computer cannot "see" the entire array at once the way a human might glance at a short list. It must **look at each element one at a time** — open one locker, check it, then decide what to do next.

The algorithm takes an array as input, searches through it, and returns:
- **`true`** — if the target value (50) is found
- **`false`** — if the target value is not in the array

```
array → [search algorithm] → true or false
```

---

## 4️⃣ Linear Search — The Simplest Approach

**Linear search** checks every element from left to right, one by one, until it either finds the target or runs out of elements.

### Pseudocode (human-readable version)
```
For each door from left to right
    If 50 is behind door
        Return true
Return false
```

- **Clarification:** This is **pseudocode** — a human-readable description of an algorithm. It's not valid C code, but it captures the logic precisely. Pseudocode is the bridge between your idea and actual code.

### More precise pseudocode (closer to code)
```
For i from 0 to n-1
    If 50 is behind doors[i]
        Return true
Return false
```

- **Clarification:** `n` is the total number of elements in the array. `i` is the index variable, going from `0` (first element) to `n-1` (last element). `doors[i]` means "the value stored at position `i`."

### Step-by-step walkthrough

**Example: searching for `50` in the array `{4, 8, 15, 16, 23, 42, 50}`**

```
Array:   [4]  [8]  [15]  [16]  [23]  [42]  [50]
Index:    0    1     2     3     4     5     6
```

| Step | `i` | `doors[i]` | Is it 50? | Action |
|------|-----|-----------|-----------|--------|
| 1 | 0 | `4` | No | Continue |
| 2 | 1 | `8` | No | Continue |
| 3 | 2 | `15` | No | Continue |
| 4 | 3 | `16` | No | Continue |
| 5 | 4 | `23` | No | Continue |
| 6 | 5 | `42` | No | Continue |
| 7 | 6 | `50` | **Yes!** | **Return true** |

Result: `true` — found in 7 steps.

**Example: searching for `99` in the same array**

| Step | `i` | `doors[i]` | Is it 99? | Action |
|------|-----|-----------|-----------|--------|
| 1 | 0 | `4` | No | Continue |
| 2 | 1 | `8` | No | Continue |
| 3 | 2 | `15` | No | Continue |
| 4 | 3 | `16` | No | Continue |
| 5 | 4 | `23` | No | Continue |
| 6 | 5 | `42` | No | Continue |
| 7 | 6 | `50` | No | Continue |

Loop ends (checked all elements) → **Return false**

Result: `false` — checked all 7 elements and didn't find 99.

### Where does `Return false` go?

Notice that `Return false` is **outside** the loop — it's only reached after every single element has been checked. This is critical:
- `Return true` is **inside** the loop — we exit immediately when we find the target (no need to check the rest).
- `Return false` is **after** the loop — we can only conclude "not found" after checking every element.

```
For i from 0 to n-1           ← loop starts
    If 50 is behind doors[i]
        Return true            ← INSIDE: found it, stop immediately
Return false                   ← OUTSIDE: loop finished, never found it
```

If you put `Return false` inside the loop, the algorithm would give up after checking just the first element — a common beginner bug.

---

## 5️⃣ Linear Search — Performance Analysis

### Best case
The target is the **first element** → only 1 check needed.

### Worst case
The target is the **last element** or **not in the array** → must check all `n` elements.

### Average case
On average, you'll check about `n/2` elements before finding the target (if it exists).

### Growth rate
Linear search runs in **O(n)** time — the red line on the graph from Image 1. As the array gets bigger, the time to search grows proportionally.

| Array size | Worst-case checks |
|------------|-------------------|
| 7 | 7 |
| 100 | 100 |
| 1,000 | 1,000 |
| 1,000,000 | 1,000,000 |

- **Clarification — why "linear"?** The name comes from the graph shape: a straight line. Time increases at a constant rate as the problem grows. For every additional element in the array, you need (at most) one additional check.
- **Clarification — is linear search bad?** Not necessarily. It's the simplest algorithm, works on **any** array (sorted or not), and for small arrays, the difference is negligible. But for large datasets, it becomes impractical. That's why we'll learn **binary search** next — which requires a sorted array but achieves logarithmic (log₂ n) performance.

---

## 6️⃣ Summary

### Key concepts
| Concept | Meaning |
|---------|---------|
| Algorithm | A step-by-step procedure (black box) that takes input → produces output |
| Efficiency | How the time/work grows as the problem size grows |
| Linear (n) | Time grows proportionally to input size — double the input, double the time |
| Logarithmic (log₂ n) | Time grows very slowly — double the input, only +1 step |
| Pseudocode | Human-readable description of an algorithm — not real code, but captures the logic |
| Linear search | Check each element from left to right, one at a time |

### Linear search
| Aspect | Detail |
|--------|--------|
| Strategy | Check every element in order, left to right |
| Best case | Target is the first element → 1 step |
| Worst case | Target is last or absent → n steps |
| Growth rate | O(n) — linear |
| Requirement | Works on **any** array (sorted or unsorted) |
| Returns | `true` if found, `false` if not |

### The efficiency graph — what to remember
| Curve | Growth | Real-world feel |
|-------|--------|-----------------|
| 🔴 **n** | Linear | Slow for large inputs — but predictable |
| 🟡 **n/2** | Linear (halved) | A bit faster, but same fundamental shape |
| 🟢 **log₂ n** | Logarithmic | Blazingly fast even for millions of items |

The takeaway: choosing the right algorithm matters more than having a faster computer. A logarithmic algorithm on a slow machine will beat a linear algorithm on a fast machine for large enough inputs.
