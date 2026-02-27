🔀 Merge Sort (Lecture 3 — Algorithms @ CS50)

We've seen two O(n²) sorting algorithms — selection sort and bubble sort. Now we use **recursion** to break through that barrier. Merge sort is a fundamentally different kind of algorithm: instead of sorting in place by swapping neighbours or finding minimums, it **divides the array in half repeatedly**, sorts each half independently, then **merges** the two sorted halves back together. The result is O(n log n) — dramatically faster for large inputs.

---

## 1. 💡 The Core Idea — Divide, Conquer, Merge

Merge sort is built on a simple recursive insight:

> **A single-element array is always sorted.** Therefore, split the array down to individual elements, then merge them back up in sorted order.

Three steps, repeated recursively:
1. **Divide** — split the array into two halves
2. **Conquer** — recursively sort each half (by applying merge sort to each)
3. **Merge** — combine the two sorted halves into one sorted array

The magic is in the merge step: merging two already-sorted arrays into one sorted array is an O(n) operation — you just walk through both simultaneously, always picking the smaller front element.

---

## 2. 📝 Pseudocode

```
If only one number
    Quit
Else
    Sort left half of numbers
    Sort right half of numbers
    Merge sorted halves
```

### Breaking it down

| Line | Meaning |
|------|---------|
| `If only one number` | **Base case** — a single element is already sorted. Stop. |
| `Quit` | Return — nothing to do. |
| `Sort left half` | **Recursive case** — apply the exact same algorithm to the left half |
| `Sort right half` | **Recursive case** — apply the exact same algorithm to the right half |
| `Merge sorted halves` | Combine two sorted arrays into one sorted array |

- **Clarification — why does this work?** Both halves are sorted before we merge. When merging, we compare the front elements of each half and always take the smaller one. Since both halves are already in order, the front of each half is always its current smallest — so this greedy pick always produces the correct result.

---

## 3. 🔁 Full Step-by-Step Example

### Array: `{6, 3, 4, 1}` → goal: sort to `{1, 3, 4, 6}`

---

**Step 1 — Is it one number?**
```
{6, 3, 4, 1}     → 4 numbers → No → proceed
```

**Step 2 — Split into two halves:**
```
Left:  {6, 3}
Right: {4, 1}
```

**Step 3 — Sort left half `{6, 3}` (recursion begins):**

> Is `{6, 3}` one number? No → split again.
> ```
> Left:  {6}
> Right: {3}
> ```
> - Is `{6}` one number? **Yes → Quit.** (6 is sorted)
> - Is `{3}` one number? **Yes → Quit.** (3 is sorted)
>
> **Merge `{6}` and `{3}`:**
> - Compare front of left (6) vs front of right (3)
> - 3 < 6 → take 3 first
> - Only 6 remains → take 6
> - Result: `{3, 6}` ✅

Left half is now: `{3, 6}`

**Step 4 — Sort right half `{4, 1}` (recursion begins):**

> Is `{4, 1}` one number? No → split again.
> ```
> Left:  {4}
> Right: {1}
> ```
> - Is `{4}` one number? **Yes → Quit.** (4 is sorted)
> - Is `{1}` one number? **Yes → Quit.** (1 is sorted)
>
> **Merge `{4}` and `{1}`:**
> - Compare front of left (4) vs front of right (1)
> - 1 < 4 → take 1 first
> - Only 4 remains → take 4
> - Result: `{1, 4}` ✅

Right half is now: `{1, 4}`

**Step 5 — Merge the two sorted halves `{3, 6}` and `{1, 4}`:**

| Step | Left pointer | Right pointer | Compare | Take | Result so far |
|------|-------------|--------------|---------|------|---------------|
| 1 | 3 | 1 | 3 vs 1 → 1 smaller | **1** from right | {1} |
| 2 | 3 | 4 | 3 vs 4 → 3 smaller | **3** from left | {1, 3} |
| 3 | 6 | 4 | 6 vs 4 → 4 smaller | **4** from right | {1, 3, 4} |
| 4 | 6 | (empty) | Right exhausted | **6** from left | {1, 3, 4, 6} |

**Final result: `{1, 3, 4, 6}` ✅**

---

## 4. 🌳 The Recursive Tree — Visualising All Splits and Merges

This tree shows every step. Splits go downward; merges go upward on the right side:

```
                  {6, 3, 4, 1}
                 /             \
            {6, 3}            {4, 1}
           /      \           /    \
         {6}      {3}       {4}    {1}
          ↓        ↓         ↓      ↓
        (base)  (base)    (base)  (base)
           \      /           \    /
           {3, 6}             {1, 4}
                  \          /
                  {1, 3, 4, 6}
```

- **Splits:** Each level doubles the number of sub-arrays (1 → 2 → 4)
- **Merges:** Each level halves the number of sub-arrays (4 → 2 → 1)
- **Levels:** For n=4 elements, there are log₂(4) = 2 levels of splitting

---

## 5. ⏱️ Running Time Analysis

### Why O(n log n)?

Two components:

**Component 1 — How many levels?**

Each split divides the array in half. Starting from n elements:
```
Level 0:  1 array of n elements
Level 1:  2 arrays of n/2 elements
Level 2:  4 arrays of n/4 elements
...
Level k:  n arrays of 1 element each (base case)
```

The number of levels is **log₂ n** — because we keep halving until we reach 1.

| n | Levels (log₂ n) |
|---|----------------|
| 4 | 2 |
| 8 | 3 |
| 16 | 4 |
| 1,024 | 10 |
| 1,048,576 | 20 |

**Component 2 — How much work per level?**

At each level, the merge step processes every element exactly once across all the merges at that level. Whether it's one big merge or many small merges, the total elements touched at any given level is always **n**.

```
Level 2 (merging pairs):     {3,6} ← 2 elements   +   {1,4} ← 2 elements  = n=4 total
Level 1 (final merge):       {1,3,4,6} ← 4 elements                         = n=4 total
```

**Combining both:**

```
Total work = (work per level) × (number of levels)
           = n × log₂ n
           = O(n log n)
```

---

## 6. 🔬 Worst Case vs. Best Case

| Case | Scenario | Steps | Notation |
|------|----------|-------|---------|
| **Worst case** | Any ordering (including reverse sorted) | n log n | O(n log n) |
| **Best case** | Already sorted | n log n | Ω(n log n) |
| **Tight bound** | Best = worst | n log n | **Θ(n log n)** |

### Why is the best case also Ω(n log n)?

Even if the array is already perfectly sorted, merge sort **still divides all the way down** to individual elements, and **still merges them back up**. It has no way to detect "I'm already sorted" and quit early like bubble sort does.

Every element must be visited at every level of the merge — there's no shortcut. So unlike bubble sort's Ω(n), merge sort always does Θ(n log n) work.

- **Clarification — is this a downside?** Not really. Even the "worst" case for merge sort (n log n) is dramatically better than the best case of selection sort (n²) for large n. Merge sort's consistency is actually a strength.

---

## 7. ⚖️ All Sorting Algorithms — Final Comparison

| Algorithm | Best case Ω | Worst case O | Tight bound Θ | In-place? |
|-----------|------------|-------------|--------------|-----------|
| Selection sort | Ω(n²) | O(n²) | Θ(n²) | ✅ Yes |
| Bubble sort | **Ω(n)** | O(n²) | ❌ None | ✅ Yes |
| **Merge sort** | **Ω(n log n)** | **O(n log n)** | **Θ(n log n)** | ❌ No* |

*Merge sort requires extra memory to hold the merged output — it cannot sort purely within the original array.

### Concrete step counts at scale

| n | Selection sort (n²) | Bubble sort worst (n²) | Merge sort (n log n) |
|---|--------------------|-----------------------|---------------------|
| 10 | 100 | 100 | ~33 |
| 100 | 10,000 | 10,000 | ~664 |
| 1,000 | 1,000,000 | 1,000,000 | ~9,966 |
| 1,000,000 | 1,000,000,000,000 | 1,000,000,000,000 | ~19,931,568 |

At n = 1,000,000: merge sort takes ~20 million steps. Selection/bubble sort take **1 trillion**. Merge sort is roughly 50,000× faster.

---

## 8. 🔗 The Connection to Recursion

Merge sort is the payoff for learning recursion in the previous file. The pseudocode:

```
Sort left half of numbers   ← calling merge sort on a smaller input
Sort right half of numbers  ← calling merge sort on a smaller input
Merge sorted halves         ← the O(n) merge step
```

This is recursion in its most powerful form — divide and conquer. The function calls itself twice on half-sized problems. Each of those calls does the same. The base case (`If only one number → Quit`) ensures it terminates.

Without recursion, expressing this algorithm cleanly would require complex manual stack management. With recursion, the call stack handles all the bookkeeping automatically.

---

## 9. 📌 Summary

### Merge sort algorithm

```
If only one number → Quit (base case)
Else:
    Sort left half   (recursive call)
    Sort right half  (recursive call)
    Merge sorted halves
```

### Key concepts

| Concept | Detail |
|---------|--------|
| Strategy | Divide array in half, sort halves recursively, merge |
| Base case | Array of 1 element — already sorted |
| Merge operation | Walk both sorted halves simultaneously, always pick the smaller front element |
| Levels of recursion | log₂ n splits to reach single elements |
| Work per level | n elements processed across all merges at each level |

### Running time

| | Notation | Why |
|-|----------|-----|
| Worst case | O(n log n) | Always divides log n times, processes n elements per level |
| Best case | Ω(n log n) | No early exit — must always complete all splits and merges |
| Tight bound | **Θ(n log n)** | Best = worst |

### Deriving O(n log n)

```
Levels of splitting = log₂ n
Work per level      = n (each element touched once across all merges)
Total work          = n × log₂ n = O(n log n)
```

### The trade-off

| | Merge Sort | Selection / Bubble Sort |
|-|-----------|------------------------|
| Speed | O(n log n) ✅ | O(n²) ❌ |
| Memory | Needs extra space for merging ❌ | Sorts in-place, no extra memory ✅ |
| Detects sorted? | No | Bubble sort: yes (Ω(n)) |
| Best for | Large datasets | Very small arrays or nearly-sorted data |
