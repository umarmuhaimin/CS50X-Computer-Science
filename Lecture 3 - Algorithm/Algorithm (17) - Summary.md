📋 Lecture 3 Summary — Algorithms @ CS50

This lesson was about **algorithmic thinking**: how to measure, compare, and choose algorithms based on their efficiency. You also saw how combining data with custom types (structs) and expressing complex logic (recursion) opens the door to dramatically more powerful algorithms.

---

## What You Learned in Lecture 3

---

### 1. 🧭 Algorithms

An **algorithm** is a step-by-step set of instructions for completing a task. The key insight of this lecture: two correct algorithms can solve the same problem with vastly different efficiency. Correctness alone is not enough — a good engineer thinks about how *fast* and how *scalable* a solution is.

| Topic covered | Key file |
|--------------|----------|
| What algorithms are | Algorithm (00) |
| Linear search — iterate through every element | Algorithm (00), (03), (11) |
| Binary search — divide and conquer on sorted arrays | Algorithm (01), (12) |
| Searching through structs | Algorithm (05) |

---

### 2. 📈 Big O Notation

**Big O notation** is the language used to describe how an algorithm's running time grows as the input size grows. Three notations capture different aspects:

| Notation | Meaning | Describes |
|----------|---------|-----------|
| O(n) | Upper bound | Worst case — the most steps it could ever take |
| Ω(n) | Lower bound | Best case — the fewest steps in the ideal scenario |
| Θ(n) | Tight bound | Best case = worst case — always the same |

**The Big O hierarchy** (fastest → slowest):

```
O(1)  <  O(log n)  <  O(n)  <  O(n log n)  <  O(n²)
```

**Asymptotic simplification rules:**
- Drop constants: O(3n) → O(n)
- Drop lower-order terms: O(n² + n) → O(n²)
- Only the dominant term matters for large n

| Key file |
|----------|
| Algorithm (02) |

---

### 3. 🔍 Binary Search and Linear Search

| Algorithm | Requirement | Best case | Worst case | How it works |
|-----------|------------|-----------|------------|--------------|
| **Linear search** | None | Ω(1) | O(n) | Check every element left to right |
| **Binary search** | Sorted array | Ω(1) | O(log n) | Repeatedly halve the search area |

- **Linear search** works on any array and stops the moment it finds the target. If the target is absent, it must check every element.
- **Binary search** can eliminate half the remaining elements in one step — but only because the array is sorted, making it safe to discard one half entirely.
- The trade-off: sorting costs time upfront, but for repeated searches on large datasets, binary search pays back that cost many times over.

| Key files |
|----------|
| Algorithm (00), (01), (03), (11), (12) |

---

### 4. 🔃 Sorting Algorithms

Searching fast requires a sorted array. Three sorting algorithms were covered:

| Algorithm | Best Ω | Worst O | Tight Θ | Can detect sorted? | In-place? |
|-----------|--------|---------|---------|-------------------|-----------|
| **Selection sort** | Ω(n²) | O(n²) | Θ(n²) | ❌ No | ✅ Yes |
| **Bubble sort** | **Ω(n)** | O(n²) | None | ✅ Yes (swap counter) | ✅ Yes |
| **Merge sort** | **Ω(n log n)** | **O(n log n)** | **Θ(n log n)** | ❌ No | ❌ No |

**Selection sort** — find the minimum, move it to the front, repeat. Always Θ(n²) regardless of input.

**Bubble sort** — compare adjacent pairs, swap if out of order. If a full pass has zero swaps, the array is sorted → quit early. Best case Ω(n).

**Merge sort** — recursively divide to single elements, then merge back in sorted order. Always Θ(n log n) — faster than the others for large inputs, at the cost of extra memory.

**Concrete scale comparison:**

| n | Selection/Bubble (n²) | Merge sort (n log n) | Speed gain |
|---|----------------------|---------------------|------------|
| 1,000 | ~1,000,000 steps | ~9,966 steps | ~100× faster |
| 1,000,000 | ~10¹² steps | ~19,931,568 steps | ~50,000× faster |

| Key files |
|----------|
| Algorithm (06), (07), (09), (13), (14), (16) |

---

### 5. 🔄 Recursion

**Recursion** is when a function calls itself as part of its own definition. Every recursive function needs:

| Part | Purpose | Missing it causes |
|------|---------|-------------------|
| **Base case** | Stops the recursion — simplest solvable instance | Infinite loop → stack overflow → crash |
| **Recursive case** | Calls itself with a smaller input, progressing toward the base | Never terminates |

**Key examples:**
- **Factorial:** `fact(n) = n × fact(n-1)`, base case `fact(1) = 1`
- **Fibonacci:** two base cases (`fib(1) = 0`, `fib(2) = 1`), one recursive case
- **Collatz:** one base case (`n == 1`), two recursive cases (even vs odd n)

Recursion is not just a stylistic choice — it is what makes **divide-and-conquer algorithms** like merge sort expressible cleanly. Without recursion, merge sort's "sort each half" logic would require complex manual stack management.

| Key files |
|----------|
| Algorithm (08), (10), (15) |

---

## Quick Reference — All Running Times

| Algorithm | Best Ω | Worst O | Notes |
|-----------|--------|---------|-------|
| Constant time | Ω(1) | O(1) | Same cost regardless of n |
| Linear search | Ω(1) | O(n) | Check every element |
| Binary search | Ω(1) | O(log n) | Requires sorted array |
| Selection sort | Ω(n²) | O(n²) | Always Θ(n²) |
| Bubble sort | **Ω(n)** | O(n²) | Early exit if no swaps |
| Merge sort | Ω(n log n) | O(n log n) | Always Θ(n log n); needs extra memory |
