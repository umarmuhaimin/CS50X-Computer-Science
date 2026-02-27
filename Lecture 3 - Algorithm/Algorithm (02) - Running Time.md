⏱️ Running Time / Time Complexity (Lecture 3 — Algorithms @ CS50)

When we design or choose an algorithm, one of the most important questions we ask is: **how long does it take to run?** Not in seconds on a specific machine — hardware speeds differ and change over time. Instead, computer scientists use a formal notation to describe how an algorithm's time requirement **grows** as the input gets larger. This is called **running time analysis**, and the language we use for it is **Big O notation**.

---

## 1. 📈 The Big O Graph — Formalising the Curves

**(Image 1)**

This is the same shape of graph from Lecture 0, but now the curves have **official names** written in Big O notation:

| Curve | Big O notation | Algorithm example |
|-------|---------------|------------------|
| 🔴 Red (steepest) | **O(n)** | Linear search |
| 🟡 Yellow (middle) | **O(n/2)** → simplified to **O(n)** | Linear search, two pages at a time |
| 🟢 Green (flattest) | **O(log₂ n)** | Binary search |

### The key rule about Big O: constants are dropped

Notice that O(n) and O(n/2) are treated as the **same class** — both are just written as **O(n)**. Why?

Big O is about the **shape of the curve** as the input grows toward infinity — not about the exact count of steps. A constant like `1/2` doesn't change the fundamental growth pattern. Whether you do `n` steps or `n/2` steps, if you double the input, you roughly double the time. That's what matters. The constant multiplier becomes negligible for very large `n`.

**Examples of dropping constants:**
| Actual steps | Big O notation |
|-------------|---------------|
| n | O(n) |
| n/2 | O(n) |
| 3n | O(n) |
| n + 100 | O(n) |
| n² + n | O(n²) — only the dominant term kept |
| 5 · log n | O(log n) |

- **Clarification — why drop constants?** Imagine algorithm A takes `n` steps and algorithm B takes `n/2` steps. For n = 1,000,000, A takes 1,000,000 steps and B takes 500,000. That's 2× faster — sounds huge. But both are astronomically slower than an O(log n) algorithm that takes ~20 steps. Big O focuses on which **class** an algorithm belongs to, not fine-grained differences within the same class.

---

## 2. 🏆 The Full Hierarchy of Common Running Times

Listed from **slowest to fastest** (worst to best):

```
O(n²)       ← slowest
O(n log n)
O(n)
O(log n)
O(1)        ← fastest
```

### Each one explained

**O(1) — Constant time** ⚡
- The algorithm always takes the **same number of steps**, no matter how large the input.
- Doubling the input does nothing to the running time.
- Example: accessing an array element by index (`arr[3]`) — you jump straight to it, no searching.
- Example: checking if a number is even (`n % 2 == 0`) — one operation regardless of how big `n` is.

**O(log n) — Logarithmic time** 🟢
- Time grows very slowly — each step cuts the problem in half.
- Doubling the input adds just **one more step**.
- Example: **binary search** — with 1,000,000 elements, worst case is ~20 steps. With 2,000,000 elements, worst case is ~21 steps.

**O(n) — Linear time** 🟡/🔴
- Time grows proportionally with input size.
- Doubling the input **doubles** the time.
- Example: **linear search** — with 1,000,000 elements, worst case is 1,000,000 checks.

**O(n log n) — Linearithmic time**
- Slightly worse than linear, but much better than quadratic.
- Example: **merge sort** (which we'll cover later this week) — the gold standard for sorting algorithms.
- The `n` comes from having to process every element; the `log n` comes from the divide-and-conquer splitting.

**O(n²) — Quadratic time** 🔴 (slowest in this list)
- Time grows with the **square** of the input.
- Doubling the input **quadruples** the time.
- Example: **bubble sort** and **selection sort** (covered later this week).
- For n = 1,000: 1,000,000 steps. For n = 10,000: 100,000,000 steps.
- For large inputs, this becomes impractically slow.

### Side-by-side comparison table

| n (input size) | O(1) | O(log n) | O(n) | O(n log n) | O(n²) |
|----------------|------|---------|------|-----------|-------|
| 1 | 1 | 0 | 1 | 0 | 1 |
| 10 | 1 | ~3 | 10 | ~33 | 100 |
| 100 | 1 | ~7 | 100 | ~664 | 10,000 |
| 1,000 | 1 | ~10 | 1,000 | ~9,966 | 1,000,000 |
| 1,000,000 | 1 | ~20 | 1,000,000 | ~19,931,568 | 1,000,000,000,000 |

- **Clarification:** Look at n = 1,000,000. O(1) = 1 step. O(log n) = 20 steps. O(n) = 1 million steps. O(n²) = **1 trillion steps**. The differences are staggering. This is why choosing the right algorithm matters more than having the fastest hardware.

---

## 3. 🔠 The Three Greek Letters — O, Ω, and Θ

Big O alone only tells part of the story. Computer scientists use three related notations to fully describe an algorithm's behaviour:

### O — Big O (upper bound / worst case)

Big O describes the **worst-case** running time. It's a ceiling — the algorithm will never be *worse* than this.

| Algorithm | O notation | What it means |
|-----------|-----------|---------------|
| Linear search | O(n) | In the worst case, checks every element — target is last or absent |
| Binary search | O(log n) | In the worst case, keeps halving until one element is left |

Think of Big O as the answer to: *"How bad can this get?"*

### Ω — Big Omega (lower bound / best case)

Omega describes the **best-case** running time. It's a floor — the algorithm will never be *better* than this.

| Algorithm | Ω notation | Scenario |
|-----------|-----------|----------|
| Linear search | Ω(1) | Target is the very first element — found in 1 step |
| Binary search | Ω(1) | Target is the middle element on the first check — found immediately |

Think of Omega as the answer to: *"How good can this get?"*

### Θ — Big Theta (tight bound / best = worst case)

Theta is used when the **best case and worst case are the same**. This means the algorithm always takes roughly the same amount of time, regardless of the specific input.

**Example:** Counting the number of elements in an array by iterating through it.
- Best case: still have to go through all n elements.
- Worst case: still have to go through all n elements.
- Therefore: Θ(n) — it's always n steps, no matter what.

**Another example:**
- If an algorithm has O(n) and Ω(n), then Θ(n) — the upper and lower bounds meet.
- If an algorithm has O(n) but Ω(1), there is **no** Theta — best and worst differ.

### Summary table

| Notation | Symbol | Represents | Question answered |
|----------|--------|-----------|-------------------|
| Big O | O | Upper bound (worst case) | "How bad can it get?" |
| Big Omega | Ω | Lower bound (best case) | "How good can it get?" |
| Big Theta | Θ | Tight bound (best = worst) | "Is it always the same?" |

### Applied to algorithms you've seen

| Algorithm | Best case Ω | Worst case O | Theta Θ? |
|-----------|------------|-------------|---------|
| Linear search | Ω(1) — target is first | O(n) — target is last or absent | ❌ No — they differ |
| Binary search | Ω(1) — target is middle | O(log n) — target at edge | ❌ No — they differ |

---

## 4. 📐 Asymptotic Notation — The Big Picture

All three notations (O, Ω, Θ) fall under the umbrella of **asymptotic notation**.

"Asymptotic" means: *analysing behaviour as the input size grows toward infinity.*

### Why "asymptotic"?

For small inputs, the constant factors and lower-order terms actually matter. A poorly optimised O(n) algorithm might genuinely be slower than a well-optimised O(n²) algorithm for n = 5. But as n gets large — hundreds, millions, billions — the growth class dominates completely. The asymptotic behaviour is what defines an algorithm's scalability.

### What we ignore in asymptotic analysis

| We ignore | Why |
|-----------|-----|
| Constant multipliers (`3n` vs `n`) | Don't change the growth shape |
| Lower-order terms (`n² + n` → `n²`) | Dominated by the leading term for large n |
| Hardware speed | We want machine-independent analysis |
| Programming language overhead | Same reason — pure algorithmic analysis |

### The practical impact

As inputs scale to real-world sizes (millions of web requests, billions of database records), asymptotic behaviour is what determines whether your program finishes in milliseconds, minutes, or never. This is the foundation for everything in algorithm design:

```
A faster computer running O(n²) will eventually lose to
a slower computer running O(n log n), for large enough n.
```

- **Clarification — "you will explore these in detail in future courses":** CS50 introduces these ideas at an intuitive level. In later CS courses (Data Structures, Algorithms, Theory of Computation), you'll learn formal mathematical proofs, limits, and more complex notation. For now, focus on understanding the *shapes* and being able to classify algorithms you encounter.

---

## 5. 📌 Summary

### The running time hierarchy (slowest → fastest)
| Notation | Name | Growth feel |
|----------|------|-------------|
| O(n²) | Quadratic | Steps explode — input × 2 means steps × 4 |
| O(n log n) | Linearithmic | Slightly worse than linear — used in good sorting |
| O(n) | Linear | Steps grow with input — double input, double steps |
| O(log n) | Logarithmic | Steps barely grow — double input, +1 step |
| O(1) | Constant | Steps never change — instant regardless of size |

### Three notations
| Symbol | Meaning | Focus |
|--------|---------|-------|
| **O(f(n))** | Upper bound | Worst case — ceiling |
| **Ω(f(n))** | Lower bound | Best case — floor |
| **Θ(f(n))** | Tight bound | Best = worst — exact characterisation |

### Algorithms covered so far
| Algorithm | O (worst) | Ω (best) | Θ? |
|-----------|-----------|---------|-----|
| Linear search | O(n) | Ω(1) | No |
| Binary search | O(log n) | Ω(1) | No |

### Key rules for Big O
1. **Drop constants:** O(n/2) → O(n), O(3n) → O(n)
2. **Drop lower-order terms:** O(n² + n) → O(n²)
3. **It's about the shape**, not the exact step count
4. **Worst case only** — the ceiling, not the typical or best
