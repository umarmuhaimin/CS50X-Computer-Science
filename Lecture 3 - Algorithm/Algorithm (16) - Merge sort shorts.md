🔀 Merge Sort — Doug Lloyd's Short (Lecture 3 @ CS50)

Bubble sort, selection sort, and insertion sort all share the same worst-case runtime: O(n²). Can we do better? Yes — using **merge sort**, which achieves O(n log n) by exploiting a simple but powerful insight: instead of trying to sort one big array in place, think of it as six one-element arrays, and just combine them back in the correct order. This is divide and conquer applied to sorting, and it leverages recursion to express it cleanly.

---

## 1. 🧠 The Core Idea

Instead of sorting a large array directly:
1. **Divide** the array in half, recursively, until every piece is a single element
2. **A single element is already sorted** — this is the base case
3. **Merge** pairs of sorted pieces back together in the correct order
4. Keep merging upward until the entire array is reconstructed — sorted

```
Think of {5, 2, 1, 3, 6, 4} not as one 6-element array
but as six 1-element arrays: {5} {2} {1} {3} {6} {4}
Then merge them back together in sorted order.
```

The key operation is the **merge step**: given two already-sorted arrays, merge them into one sorted array by walking through both simultaneously and always picking the smaller front element.

---

## 2. 📝 Pseudocode

```
Sort the left half of the array
Sort the right half of the array
Merge the two sorted halves together
```

That's it — three steps. The magic is that "sort the left half" and "sort the right half" are **recursive calls** to the same algorithm. The base case is implicit: a single-element array is already sorted and needs no further splitting.

| Step | Meaning |
|------|---------|
| `Sort left half` | Apply merge sort recursively to the left half |
| `Sort right half` | Apply merge sort recursively to the right half |
| `Merge sorted halves` | Walk through both sorted halves, always picking the smaller front element |

- **Clarification — how do I "sort the left half" without knowing how?** You don't need to know — you just call the same algorithm on a smaller input. Recursion handles the rest. Each call reduces the array by half until you reach a single element, which is trivially sorted.

---

## 3. 🔁 Full Step-by-Step Walkthrough

### Array: `{5, 2, 1, 3, 6, 4}` — goal: `{1, 2, 3, 4, 5, 6}`

We label halves with colours to track where we are:
- Full array = **red** (brick red)
- Left half of red = **purple**
- Right half of red = **red** (still)

---

### Phase 1 — Sort the Left Half (purple = `{5, 2, 1}`)

**Step 1: Sort the left half of purple → `{5}`**

```
Left of purple = {5}  → single element → already sorted ✅
```

**Step 2: Sort the right half of purple → `{2, 1}`**

```
Right of purple = {2, 1}  → two elements → recurse again
    Left of {2,1} = {2} → single element → already sorted ✅
    Right of {2,1} = {1} → single element → already sorted ✅
```

**Step 3: Merge `{2}` and `{1}` → `{1, 2}`**

| Compare | Decision | Result so far |
|---------|----------|--------------|
| 2 vs 1 | 1 is smaller → take **1** | {1} |
| 2 vs (empty) | Only 2 remains → take **2** | {1, 2} |

Right half of purple is now `{1, 2}`.

**Step 4: Merge purple's two sorted halves `{5}` and `{1, 2}` → `{1, 2, 5}`**

| Compare | Decision | Result so far |
|---------|----------|--------------|
| 5 vs 1 | 1 is smaller → take **1** | {1} |
| 5 vs 2 | 2 is smaller → take **2** | {1, 2} |
| 5 vs (empty) | Only 5 remains → take **5** | {1, 2, 5} |

**Left half of full array is now sorted: `{1, 2, 5}` ✅**

---

### Phase 2 — Sort the Right Half (red = `{3, 6, 4}`)

**Step 5: Sort the left half of the right section → `{3}`**

```
Left of {3,6,4} = {3}  → single element → already sorted ✅
```

**Step 6: Sort the right half of the right section → `{6, 4}`**

```
Right = {6, 4}  → two elements → recurse again
    Left of {6,4} = {6} → single element → already sorted ✅
    Right of {6,4} = {4} → single element → already sorted ✅
```

**Step 7: Merge `{6}` and `{4}` → `{4, 6}`**

| Compare | Decision | Result so far |
|---------|----------|--------------|
| 6 vs 4 | 4 is smaller → take **4** | {4} |
| 6 vs (empty) | Only 6 remains → take **6** | {4, 6} |

**Step 8: Merge `{3}` and `{4, 6}` → `{3, 4, 6}`**

| Compare | Decision | Result so far |
|---------|----------|--------------|
| 3 vs 4 | 3 is smaller → take **3** | {3} |
| (empty) vs {4, 6} | Left exhausted → take all of right | {3, 4, 6} |

**Right half of full array is now sorted: `{3, 4, 6}` ✅**

---

### Phase 3 — Final Merge: `{1, 2, 5}` and `{3, 4, 6}` → `{1, 2, 3, 4, 5, 6}`

| Step | Left pointer | Right pointer | Compare | Take | Result so far |
|------|-------------|--------------|---------|------|---------------|
| 1 | 1 | 3 | 1 vs 3 → 1 smaller | **1** from left | {1} |
| 2 | 2 | 3 | 2 vs 3 → 2 smaller | **2** from left | {1, 2} |
| 3 | 5 | 3 | 5 vs 3 → 3 smaller | **3** from right | {1, 2, 3} |
| 4 | 5 | 4 | 5 vs 4 → 4 smaller | **4** from right | {1, 2, 3, 4} |
| 5 | 5 | 6 | 5 vs 6 → 5 smaller | **5** from left | {1, 2, 3, 4, 5} |
| 6 | (empty) | 6 | Left exhausted | **6** from right | {1, 2, 3, 4, 5, 6} |

**Final result: `{1, 2, 3, 4, 5, 6}` ✅**

---

## 4. 🌳 The Recursive Tree — Full Picture

```
                         {5, 2, 1, 3, 6, 4}
                        /                    \
              {5, 2, 1}                      {3, 6, 4}
             /         \                   /          \
          {5}          {2, 1}           {3}          {6, 4}
           ↓           /    \            ↓           /    \
        (base)       {2}    {1}        (base)      {6}    {4}
                      ↓      ↓                      ↓      ↓
                   (base)  (base)                (base)  (base)
                       \    /                        \    /
                      {1, 2}                         {4, 6}
                         \                          /
                         {1, 2, 5}           {3, 4, 6}
                                   \        /
                               {1, 2, 3, 4, 5, 6}
```

- **Splitting:** Each level doubles the number of sub-arrays (1 → 2 → 4 → 6 single elements)
- **Merging:** Each level halves the number of sub-arrays (6 → 3 → 2 → 1)
- **Levels:** For n=6 elements, there are log₂(6) ≈ 2-3 levels of splitting

---

## 5. ⏱️ Running Time Analysis

### Worst case — O(n log n)

Two components:

**Component 1 — How many levels?**

Each split divides the problem in half. Starting from n elements, it takes log₂ n splits to reach all single-element arrays.

```
n elements → n/2 → n/4 → ... → 1 element
Number of levels = log₂ n
```

**Component 2 — How much work per level?**

At each level, the merge step touches every element exactly once across all the merges happening at that level. Whether it's one big merge or many small ones, the total is always n.

```
Level 1 merges: (n/2 + n/2) = n elements processed
Level 2 merges: (n/4 + n/4) + (n/4 + n/4) = n elements processed
...every level: n elements processed
```

**Combining:**
```
Total work = n (per level) × log n (levels)
           = O(n log n)
```

### Best case — also Ω(n log n)

Unlike bubble sort (which can detect a sorted array and quit), merge sort has no shortcut. Even if the array is already perfectly sorted, merge sort **still splits all the way down** to single elements and **still merges back up**. It has no mechanism to detect "I'm already sorted."

```
Best case: Ω(n log n)     ← same as worst case
```

> In this regard, merge sort is similar to selection sort (always does the same work). But it's different in what that work *achieves*: O(n log n) is dramatically better than O(n²).

### Tight bound — Θ(n log n)

Since best = worst:

```
Θ(n log n)
```

| Case | Scenario | Notation |
|------|----------|---------|
| **Worst** | Any ordering | O(n log n) |
| **Best** | Already sorted — but doesn't help | Ω(n log n) |
| **Tight bound** | Best = worst | **Θ(n log n)** |

---

## 6. ⚖️ The Trade-off — Speed vs. Memory

Merge sort's improvement in speed comes at a cost:

| | Merge Sort | O(n²) sorts |
|-|-----------|-------------|
| **Speed (worst)** | **O(n log n)** ✅ | O(n²) ❌ |
| **Speed (best)** | Ω(n log n) | Ω(n) (bubble) / Ω(n²) (selection) |
| **Memory** | ❌ Needs extra space to hold merged sub-arrays | ✅ Sorts in-place |
| **Can detect sorted?** | ❌ No | ✅ Bubble sort can |

The extra memory comes from needing new array segments to hold merged results during the process. You cannot merge two sorted arrays purely within the original array — you need a temporary holding space.

> When is O(n²) actually better? On very small arrays or nearly-sorted arrays, bubble sort's Ω(n) best case might beat merge sort's Ω(n log n). But for any large dataset, merge sort wins decisively.

### Concrete comparison at scale

| n | Selection/Bubble sort (n²) | Merge sort (n log n) | Ratio |
|---|--------------------------|---------------------|-------|
| 100 | 10,000 | ~664 | 15× faster |
| 1,000 | 1,000,000 | ~9,966 | 100× faster |
| 10,000 | 100,000,000 | ~132,877 | 753× faster |
| 1,000,000 | 10¹² | ~19,931,568 | ~50,000× faster |

---

## 7. 📌 Summary

### The algorithm

```
Sort left half (recursive call)
Sort right half (recursive call)
Merge the two sorted halves
```

Base case (implicit): array of 1 element — already sorted.

### Key mechanics

| Concept | Detail |
|---------|--------|
| Strategy | Divide to single elements, merge back up in sorted order |
| Base case | Single element — already sorted, no action needed |
| Merge operation | Walk two sorted arrays simultaneously, always pick the smaller front element |
| Memory cost | Requires extra space for merged sub-arrays |
| Uses recursion | Yes — the "sort left/right half" steps call merge sort on smaller inputs |

### Running time

| | Notation | Why |
|-|----------|-----|
| Worst case | O(n log n) | log n levels × n work per level |
| Best case | Ω(n log n) | No early exit — always completes all splits and merges |
| Tight bound | **Θ(n log n)** | Best = worst |

### Final comparison — all four sorting algorithms

| Algorithm | Best case Ω | Worst case O | Tight bound Θ | In-place? |
|-----------|------------|-------------|--------------|-----------|
| Selection sort | Ω(n²) | O(n²) | Θ(n²) | ✅ Yes |
| Bubble sort | **Ω(n)** | O(n²) | ❌ None | ✅ Yes |
| **Merge sort** | **Ω(n log n)** | **O(n log n)** | **Θ(n log n)** | ❌ No |

Merge sort is the most powerful general-purpose sorting algorithm covered in CS50. Understanding it requires understanding recursion — which is exactly why recursion was taught first.
