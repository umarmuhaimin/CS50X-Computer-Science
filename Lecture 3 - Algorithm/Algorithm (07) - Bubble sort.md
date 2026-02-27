🫧 Bubble Sort (Lecture 3 — Algorithms @ CS50)

Bubble sort is the second sorting algorithm we study. Where selection sort finds the minimum each pass and places it at the front, bubble sort takes a completely different approach: it repeatedly **compares neighbouring pairs** and swaps them if they're out of order. The effect is that larger values gradually "bubble up" to the right end of the array — like air bubbles rising to the surface of water.

---

## 1. 💡 The Core Idea

Picture going through the array comparing each pair of neighbours. If the left neighbour is bigger than the right, they're out of order — swap them. Repeat across the whole array. At the end of one full pass, the **largest value is guaranteed to be at the very end** — it bubbled all the way to the right.

Then do it again. This time the second-largest will bubble to the second-last position. Keep repeating until the whole array is sorted — or until a full pass produces zero swaps (meaning the array is already sorted).

```
Pass 1: largest element bubbles to position [n-1]
Pass 2: second-largest bubbles to position [n-2]
Pass 3: third-largest bubbles to position [n-3]
...
```

---

## 2. 📝 Pseudocode

```
Repeat n-1 times
    For i from 0 to n–2
        If numbers[i] and numbers[i+1] out of order
            Swap them
    If no swaps
        Quit
```

### Breaking it down

| Line | Meaning |
|------|---------|
| `Repeat n-1 times` | Outer loop — at most n-1 full passes (after n-1 passes, array must be sorted) |
| `For i from 0 to n-2` | Inner loop — compare each adjacent pair. Stop at `n-2` because we compare `[i]` with `[i+1]`, so the last pair is `[n-2]` and `[n-1]` |
| `If numbers[i] and numbers[i+1] out of order` | Check if left neighbour > right neighbour |
| `Swap them` | Exchange the two values — the bigger one moves right |
| `If no swaps` | Entire pass completed with zero swaps |
| `Quit` | Array is already sorted — no need to continue |

- **Clarification — why `i` goes to `n-2` not `n-1`:** We compare `numbers[i]` with `numbers[i+1]`. If `i` reached `n-1`, then `i+1 = n` — which is out of bounds. The last valid comparison is `numbers[n-2]` vs `numbers[n-1]`.
- **Clarification — the `If no swaps` optimisation:** Without this, bubble sort always does exactly `(n-1) × (n-1)` comparisons, even on an already-sorted array. With it, the algorithm detects a sorted state after just one pass and quits — this is what gives bubble sort its better best-case performance.

---

## 3. 🔁 Full Step-by-Step Example

### Array: `{5, 2, 7, 1, 3}` (n = 5)

---

**Pass 1 — scan all adjacent pairs:**

| Compare | In order? | Action | Array state |
|---------|----------|--------|-------------|
| `numbers[0]=5` vs `numbers[1]=2` | ❌ 5 > 2 | Swap | {**2, 5**, 7, 1, 3} |
| `numbers[1]=5` vs `numbers[2]=7` | ✅ 5 < 7 | No swap | {2, 5, 7, 1, 3} |
| `numbers[2]=7` vs `numbers[3]=1` | ❌ 7 > 1 | Swap | {2, 5, **1, 7**, 3} |
| `numbers[3]=7` vs `numbers[4]=3` | ❌ 7 > 3 | Swap | {2, 5, 1, **3, 7**} |

End of Pass 1: `{2, 5, 1, 3, 7}` — **7 has bubbled to the end** ✅
Swaps occurred → continue.

---

**Pass 2 — scan again:**

| Compare | In order? | Action | Array state |
|---------|----------|--------|-------------|
| `numbers[0]=2` vs `numbers[1]=5` | ✅ 2 < 5 | No swap | {2, 5, 1, 3, 7} |
| `numbers[1]=5` vs `numbers[2]=1` | ❌ 5 > 1 | Swap | {2, **1, 5**, 3, 7} |
| `numbers[2]=5` vs `numbers[3]=3` | ❌ 5 > 3 | Swap | {2, 1, **3, 5**, 7} |
| `numbers[3]=5` vs `numbers[4]=7` | ✅ 5 < 7 | No swap | {2, 1, 3, 5, 7} |

End of Pass 2: `{2, 1, 3, 5, 7}` — **5 has settled into place** ✅
Swaps occurred → continue.

---

**Pass 3 — scan again:**

| Compare | In order? | Action | Array state |
|---------|----------|--------|-------------|
| `numbers[0]=2` vs `numbers[1]=1` | ❌ 2 > 1 | Swap | {**1, 2**, 3, 5, 7} |
| `numbers[1]=2` vs `numbers[2]=3` | ✅ 2 < 3 | No swap | {1, 2, 3, 5, 7} |
| `numbers[2]=3` vs `numbers[3]=5` | ✅ 3 < 5 | No swap | {1, 2, 3, 5, 7} |
| `numbers[3]=5` vs `numbers[4]=7` | ✅ 5 < 7 | No swap | {1, 2, 3, 5, 7} |

End of Pass 3: `{1, 2, 3, 5, 7}` — array is now sorted ✅
Swaps occurred (1 swap) → continue.

---

**Pass 4 — scan again:**

| Compare | In order? | Action | Array state |
|---------|----------|--------|-------------|
| `numbers[0]=1` vs `numbers[1]=2` | ✅ | No swap | {1, 2, 3, 5, 7} |
| `numbers[1]=2` vs `numbers[2]=3` | ✅ | No swap | {1, 2, 3, 5, 7} |
| `numbers[2]=3` vs `numbers[3]=5` | ✅ | No swap | {1, 2, 3, 5, 7} |
| `numbers[3]=5` vs `numbers[4]=7` | ✅ | No swap | {1, 2, 3, 5, 7} |

**No swaps occurred → Quit immediately!** ✅

**Final result: `{1, 2, 3, 5, 7}`**

### Summary of all passes

| Pass | Array before | Swaps? | Array after | Largest settled |
|------|-------------|--------|-------------|-----------------|
| 1 | {5, 2, 7, 1, 3} | 3 swaps | {2, 5, 1, 3, **7**} | 7 → position 4 |
| 2 | {2, 5, 1, 3, 7} | 2 swaps | {2, 1, 3, **5**, 7} | 5 → position 3 |
| 3 | {2, 1, 3, 5, 7} | 1 swap | {**1**, 2, 3, 5, 7} | fully sorted |
| 4 | {1, 2, 3, 5, 7} | 0 swaps | **Quit** | — |

---

## 4. ⏱️ Running Time Analysis

### Counting the steps (worst case)

- Outer loop: runs at most `n - 1` times
- Inner loop: runs `n - 1` comparisons each pass

Total in worst case:

```
(n - 1) × (n - 1)
= n² - n - n + 1
= n² - 2n + 1
```

Drop lower-order terms and constants:

```
→ O(n²)
```

### Derivation shown in the raw notes

```
(n − 1) × (n − 1)
= n² − n − n + 1
= n² − 2n + 1
= O(n²)
```

---

## 5. ⚖️ Bubble Sort vs. Selection Sort — The Key Difference

This is the most important conceptual point in this file:

| | Selection Sort | Bubble Sort |
|-|---------------|-------------|
| **Worst case** | O(n²) | O(n²) |
| **Best case** | **Ω(n²)** | **Ω(n)** |
| **Tight bound Θ** | Θ(n²) — always quadratic | **No Θ** — best ≠ worst |
| **Can detect sorted array?** | ❌ No — always scans the full unsorted region | ✅ Yes — zero swaps in one pass → quit |

### Why does bubble sort achieve Ω(n)?

If the array is **already sorted**, every adjacent pair is in order — zero swaps happen in the very first pass. The `If no swaps → Quit` condition triggers immediately. The algorithm performs just one pass of `n - 1` comparisons → **Ω(n)**.

Selection sort has no equivalent escape hatch — it always scans the entire unsorted region to find the minimum, even if the array is already perfectly sorted.

### Best case example — already sorted array `{1, 2, 3, 5, 7}`

**Pass 1:**

| Compare | In order? | Swap? |
|---------|----------|-------|
| 1 vs 2 | ✅ | No |
| 2 vs 3 | ✅ | No |
| 3 vs 5 | ✅ | No |
| 5 vs 7 | ✅ | No |

No swaps → **Quit after just 1 pass** → Ω(n) ✅

---

## 6. 📌 Summary

### The algorithm

```
Repeat n-1 times:
    For i from 0 to n-2:
        If numbers[i] > numbers[i+1]: swap
    If no swaps: quit
```

### Key mechanics

| Concept | Detail |
|---------|--------|
| Compare adjacent pairs | `numbers[i]` vs `numbers[i+1]` for i = 0 to n-2 |
| Swap if out of order | Bigger value moves right, smaller moves left |
| Bubbling effect | The largest unsorted element reaches its final position after each pass |
| Early exit | If a full pass has zero swaps → array is sorted → quit |

### Running time

| Case | Notation | Explanation |
|------|---------|-------------|
| Worst | O(n²) | Array is reverse sorted — maximum swaps every pass |
| Best | Ω(n) | Array is already sorted — zero swaps on first pass, quit |
| Tight bound | **No Θ** | Best ≠ worst — the cases differ |

### Comparison with selection sort

| | Selection Sort | Bubble Sort |
|-|---------------|-------------|
| Big O (worst) | O(n²) | O(n²) |
| Omega (best) | Ω(n²) | **Ω(n)** ← advantage |
| Detects sorted? | ❌ | ✅ |
| Theta | Θ(n²) | None |
