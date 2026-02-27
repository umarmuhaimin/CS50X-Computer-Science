🫧 Bubble Sort — Doug Lloyd's Short (Lecture 3 @ CS50)

Bubble sort is a sorting algorithm that repeatedly examines every adjacent pair of elements and swaps them if they're out of order. The effect is that larger values gradually "bubble up" to the right — hence the name. It's not the fastest algorithm, but it has one clever optimisation: if a full pass produces zero swaps, the array is guaranteed to be sorted, and the algorithm can quit early.

---

## 1. 🧠 The Core Idea

Move higher-valued elements to the right and lower-valued elements to the left by repeatedly swapping adjacent neighbours that are out of order.

After each full pass through the array:
- The **largest unsorted element** has bubbled all the way to the right
- That element is now in its final sorted position and never needs to be touched again
- The sorted region grows one element at a time from the **right**

```
Pass 1: largest element bubbles to position [n-1]   ← sorted
Pass 2: second-largest bubbles to position [n-2]    ← sorted
Pass 3: third-largest bubbles to position [n-3]     ← sorted
...
```

This builds the sorted array **from right to left, largest to smallest** — the opposite of selection sort, which builds from left to right, smallest to largest.

---

## 2. 📝 Pseudocode

```
Set swap counter to a non-zero value
Repeat until swap counter is 0:
    Reset swap counter to 0
    For each adjacent pair (i and i+1) in the unsorted region:
        If they are out of order (left > right)
            Swap them
            Add 1 to the swap counter
    If swap counter is still 0
        Quit (array is sorted)
```

### Breaking it down

| Line | Meaning |
|------|---------|
| `Set swap counter to non-zero` | Forces the loop to begin — if counter started at 0, the repeat condition would be immediately satisfied and we'd never start |
| `Repeat until swap counter is 0` | Keep making passes as long as swaps are still happening |
| `Reset swap counter to 0` | At the start of each pass, assume no swaps until proven otherwise |
| `Look at each adjacent pair` | Compare `numbers[i]` and `numbers[i+1]` for every valid pair |
| `If out of order → swap` | If left > right, exchange them. Bigger value moves right. |
| `Add 1 to swap counter` | Track that a swap occurred — we can't declare sorted yet |
| `If swap counter is 0 → quit` | Entire pass with zero swaps = all pairs are in order = array is sorted |

- **Clarification — the swap counter trick:** Without the swap counter check, bubble sort always does exactly n-1 passes even on an already-sorted array. With it, the algorithm can detect a sorted array after just one pass (zero swaps → done). This is what gives bubble sort its Ω(n) best case.
- **Clarification — the initial non-zero value:** The transcript uses -1 as the initial value. It could be any non-zero value — the only requirement is that it isn't 0, so the repeat loop actually starts.

---

## 3. 🔁 Step-by-Step Example

### Array: `{5, 2, 1, 3, 6, 4}` (n = 6)

---

### Pass 1 — scan the full unsorted array, swap counter starts at 0

| Step | Compare | In order? | Action | Swap counter | Array state |
|------|---------|----------|--------|-------------|-------------|
| 1 | [0]=5 vs [1]=2 | ❌ 5 > 2 | Swap | 1 | {**2, 5**, 1, 3, 6, 4} |
| 2 | [1]=5 vs [2]=1 | ❌ 5 > 1 | Swap | 2 | {2, **1, 5**, 3, 6, 4} |
| 3 | [2]=5 vs [3]=3 | ❌ 5 > 3 | Swap | 3 | {2, 1, **3, 5**, 6, 4} |
| 4 | [3]=5 vs [4]=6 | ✅ 5 < 6 | No swap | 3 | {2, 1, 3, 5, 6, 4} |
| 5 | [4]=6 vs [5]=4 | ❌ 6 > 4 | Swap | 4 | {2, 1, 3, 5, **4, 6**} |

**End of Pass 1:** `{2, 1, 3, 5, 4, 6}` — **6 has bubbled all the way to the end** ✅
Swap counter = 4 → continue.

> Notice how 5 "tried" to bubble right but got blocked by 6. Then 6 kept swapping until it reached position 5. This cascading behaviour is why it's called bubble sort.

---

### Pass 2 — scan the unsorted region (indices 0–4), swap counter reset to 0

| Step | Compare | In order? | Action | Swap counter | Array state |
|------|---------|----------|--------|-------------|-------------|
| 1 | [0]=2 vs [1]=1 | ❌ 2 > 1 | Swap | 1 | {**1, 2**, 3, 5, 4, 6} |
| 2 | [1]=2 vs [2]=3 | ✅ 2 < 3 | No swap | 1 | {1, 2, 3, 5, 4, 6} |
| 3 | [2]=3 vs [3]=5 | ✅ 3 < 5 | No swap | 1 | {1, 2, 3, 5, 4, 6} |
| 4 | [3]=5 vs [4]=4 | ❌ 5 > 4 | Swap | 2 | {1, 2, 3, **4, 5**, 6} |

**End of Pass 2:** `{1, 2, 3, 4, 5, 6}` — **5 has settled into the sorted portion** ✅
Swap counter = 2 → continue.

> By now the array looks sorted to our eyes, but the algorithm doesn't know that yet. It hasn't been proven — it still needs to do one more pass to check.

---

### Pass 3 — scan the unsorted region (indices 0–3), swap counter reset to 0

| Step | Compare | In order? | Action | Swap counter | Array state |
|------|---------|----------|--------|-------------|-------------|
| 1 | [0]=1 vs [1]=2 | ✅ 1 < 2 | No swap | 0 | {1, 2, 3, 4, 5, 6} |
| 2 | [1]=2 vs [2]=3 | ✅ 2 < 3 | No swap | 0 | {1, 2, 3, 4, 5, 6} |
| 3 | [2]=3 vs [3]=4 | ✅ 3 < 4 | No swap | 0 | {1, 2, 3, 4, 5, 6} |

**End of Pass 3:** Swap counter = **0** → **QUIT immediately** ✅

**Final result: `{1, 2, 3, 4, 5, 6}`** ✅

### All passes at a glance

| Pass | Array before | Swaps | Array after | Largest settled |
|------|-------------|-------|-------------|-----------------|
| 1 | {5, 2, 1, 3, 6, 4} | 4 | {2, 1, 3, 5, 4, **6**} | 6 → index 5 |
| 2 | {2, 1, 3, 5, 4, 6} | 2 | {1, 2, 3, 4, **5**, 6} | 5 → index 4 |
| 3 | {1, 2, 3, 4, 5, 6} | 0 | **QUIT** | Array confirmed sorted |

---

## 4. ⏱️ Running Time Analysis

### Worst case — O(n²)

The worst case is a completely **reverse-sorted array**. Every adjacent pair is out of order, so every comparison results in a swap. Each of the n elements must bubble all the way across all the other n elements.

```
Outer loop: up to n - 1 passes
Inner loop: up to n - 1 comparisons per pass

Total: (n - 1) × (n - 1) = n² - 2n + 1 → O(n²)
```

Derivation:
```
(n − 1) × (n − 1)
= n² − n − n + 1
= n² − 2n + 1
→ drop lower-order terms and constants
= O(n²)
```

### Best case — Ω(n)

The best case is an already **sorted array**. On the very first pass, every adjacent pair is already in order — zero swaps occur. The swap counter stays at 0, and the algorithm quits after just that one pass of `n - 1` comparisons.

```
Best case: Ω(n)     ← just one pass of n-1 comparisons
```

### Why this is better than selection sort's best case

Selection sort always scans its entire unsorted region to find the minimum — even when the array is already sorted. It has no early-exit mechanism. Bubble sort's swap counter gives it exactly that mechanism.

| | Selection Sort | Bubble Sort |
|-|---------------|-------------|
| Worst case | O(n²) | O(n²) |
| **Best case** | **Ω(n²)** | **Ω(n)** ← better |
| Can detect sorted? | ❌ No | ✅ Yes (zero swaps) |
| Tight bound Θ | Θ(n²) | **No Θ** (best ≠ worst) |

---

## 5. 📌 Summary

### The algorithm

```
Set counter = non-zero
Repeat until counter == 0:
    counter = 0
    For i from 0 to n-2:
        If numbers[i] > numbers[i+1]:
            Swap numbers[i] and numbers[i+1]
            counter++
```

### Key mechanics

| Concept | Detail |
|---------|--------|
| Compare adjacent pairs | `numbers[i]` vs `numbers[i+1]`, for i = 0 to n-2 |
| Swap if out of order | Larger value moves right, smaller moves left |
| Bubbling effect | Largest unsorted element reaches its final position after each pass |
| Early exit | Zero swaps in a full pass → array confirmed sorted → quit |
| Builds sorted region | Right to left, largest to smallest |

### Running time

| Case | Notation | Explanation |
|------|---------|-------------|
| Worst | O(n²) | Reverse sorted — every comparison is a swap |
| Best | **Ω(n)** | Already sorted — zero swaps on first pass, quit immediately |
| Tight bound | **No Θ** | Best ≠ worst — the two cases are genuinely different |
