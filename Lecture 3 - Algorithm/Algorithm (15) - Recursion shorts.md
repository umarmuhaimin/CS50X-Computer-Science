🔄 Recursion — Doug Lloyd's Short (Lecture 3 @ CS50)

Recursion is one of the most elegant ideas in all of computer science. A recursive function is defined as a function that calls itself as part of its own execution. Instead of long loops and extra variables, recursion can express complex problems in just a few lines of surprisingly beautiful code. As Doug Lloyd puts it: *"these recursive procedures are going to look so short, and they really are going to make your code look a lot more beautiful."*

---

## 1. 🧠 What Is Recursion?

A **recursive function** calls **itself** as part of its definition. Instead of solving a problem all at once, it:

1. Checks if the problem is small enough to solve directly — the **base case**
2. If not, solves a **slightly smaller** version of the same problem by calling itself — the **recursive case**
3. Uses that smaller result to build the full answer

**Mental model:** Imagine n! (5 factorial). To compute 5!, you need 4!. To compute 4!, you need 3!. Each call passes the buck to a slightly smaller version — until you reach 1! which is just 1 by definition. Then all the waiting calls can finally resolve, one by one.

---

## 2. 🏛️ The Factorial Function — A Perfect Example

### What is factorial?

Factorial (written `n!` in math, `fact(n)` in code) is defined over all positive integers:

```
5! = 5 × 4 × 3 × 2 × 1 = 120
4! = 4 × 3 × 2 × 1 = 24
3! = 3 × 2 × 1 = 6
2! = 2 × 1 = 2
1! = 1
```

### Spotting the recursive pattern

Look at the definitions side by side:

| Expression | Expanded | Recursive form |
|-----------|---------|----------------|
| 1! | 1 | 1 (base case — just 1) |
| 2! | 2 × 1 | 2 × **1!** |
| 3! | 3 × 2 × 1 | 3 × **2!** |
| 4! | 4 × 3 × 2 × 1 | 4 × **3!** |
| 5! | 5 × 4 × 3 × 2 × 1 | 5 × **4!** |

The general rule:
```
n! = n × (n − 1)!
```

This is the recursive insight: the factorial of n is just n times the factorial of n minus 1. The problem expresses itself in terms of a smaller version of itself.

---

## 3. 🔑 The Two Required Parts of Every Recursive Function

Every correct recursive function **must** have both:

### Base case
The simplest, most trivially solvable instance — the case that **stops** the recursion.

- Without a base case → infinite recursion → the function keeps calling itself forever → stack overflow → program crash
- The base case is always checked **first**

For factorial: `1! = 1` — no multiplication needed. Just return 1.

### Recursive case
The case where the function calls **itself** with a **slightly smaller** input, moving closer to the base case.

- Must always reduce the problem (smaller `n`, shorter array, etc.)
- Passes the bulk of the work to the next call down

For factorial: `fact(n) = n × fact(n - 1)` — call fact with `n-1`, which is one step closer to the base case.

| Part | Purpose | What happens without it |
|------|---------|------------------------|
| Base case | Stops the recursion | Infinite loop → crash (stack overflow) |
| Recursive case | Makes progress toward the base | Never reduces the problem → no progress |

---

## 4. 💻 The Code — Recursive vs. Iterative Factorial

### Recursive version (4 lines of logic)

```c
int fact(int n)
{
    if (n == 1)
        return 1;
    else
        return n * fact(n - 1);
}
```

Or with single-line conditionals (same behaviour, more compact):

```c
int fact(int n)
{
    if (n == 1) return 1;
    return n * fact(n - 1);
}
```

#### Breaking down the recursive version

| Line | Role | Explanation |
|------|------|-------------|
| `if (n == 1) return 1;` | **Base case** | If n is 1, return 1. No multiplication. Stop. |
| `return n * fact(n - 1);` | **Recursive case** | Return n times the factorial of n-1. Calls itself with a smaller input. |

That's it. Four lines. The factorial of any positive integer is computed by those two rules.

---

### Iterative version (for comparison)

```c
int fact(int n)
{
    int product = 1;
    while (n > 0)
    {
        product *= n;
        n--;
    }
    return product;
}
```

Both functions compute the exact same result. But notice the differences:

| | Recursive | Iterative |
|-|-----------|----------|
| Extra variable | None | `product` needed |
| Loop/control | None (the call stack handles it) | `while` loop |
| Lines of logic | 2 | 4+ |
| Elegance | ✅ Concise, mirrors the math | More mechanical |
| Call stack usage | Uses call stack frames | Uses a single stack frame |

The recursive version is shorter and more directly mirrors the mathematical definition. The iterative version is explicit about every step.

---

## 5. 🔁 Call Trace — `fact(5)` Step by Step

When `fact(5)` is called, a chain of function calls builds up before any results come back:

```
fact(5)
  → not base case → return 5 * fact(4)
      → not base case → return 4 * fact(3)
          → not base case → return 3 * fact(2)
              → not base case → return 2 * fact(1)
                  → BASE CASE: n == 1 → return 1
              ← fact(1) = 1 → fact(2) = 2 * 1 = 2
          ← fact(2) = 2 → fact(3) = 3 * 2 = 6
      ← fact(3) = 6 → fact(4) = 4 * 6 = 24
  ← fact(4) = 24 → fact(5) = 5 * 24 = 120
```

### Call stack trace table

| Call | n | Waiting for | Returns |
|------|---|-------------|---------|
| fact(5) | 5 | fact(4) | 5 × 24 = **120** |
| fact(4) | 4 | fact(3) | 4 × 6 = **24** |
| fact(3) | 3 | fact(2) | 3 × 2 = **6** |
| fact(2) | 2 | fact(1) | 2 × 1 = **2** |
| fact(1) | 1 | ← BASE CASE | **1** |

The chain of calls builds down to the base case. Then results bubble back up: 1 → 2 → 6 → 24 → 120.

---

## 6. 🌿 Multiple Base Cases — Fibonacci

Recursive functions aren't limited to one base case. The **Fibonacci sequence** requires two:

```
Fibonacci sequence: 0, 1, 1, 2, 3, 5, 8, 13, 21, ...
```

| Rule | Definition |
|------|-----------|
| fib(1) = 0 | First element is 0 — by definition |
| fib(2) = 1 | Second element is 1 — by definition |
| fib(n) = fib(n-1) + fib(n-2) | Every other element is the sum of the two before it |

Why two base cases? Because the recursive case `fib(n-1) + fib(n-2)` would try to call `fib(0)` and even `fib(-1)` if we only stopped at n=1. Both 1 and 2 are stopping points that return fixed values without further recursion.

```c
int fib(int n)
{
    if (n == 1) return 0;
    if (n == 2) return 1;
    return fib(n - 1) + fib(n - 2);
}
```

Verification:
```
fib(5) = fib(4) + fib(3)
       = (fib(3) + fib(2)) + (fib(2) + fib(1))
       = ((fib(2) + fib(1)) + 1) + (1 + 0)
       = ((1 + 0) + 1) + 1
       = 2 + 1 = 3  ✅  (5th Fibonacci number is 3)
```

---

## 7. 🔀 Multiple Recursive Cases — The Collatz Conjecture

Just as we can have multiple base cases, we can have **multiple recursive cases**. The **Collatz conjecture** is the classic example.

### What is the Collatz conjecture?

Apply these steps to any positive integer — it is conjectured that you will always eventually reach 1:

| Condition | Step |
|-----------|------|
| n == 1 | Stop (base case — 0 more steps) |
| n is even | Go to n / 2 |
| n is odd | Go to 3n + 1 |

The function `collatz(n)` counts **how many steps** it takes to reach 1 starting from n.

### Test cases

| n | Steps | Path |
|---|-------|------|
| 1 | 0 | Already at 1 |
| 2 | 1 | 2 → 1 |
| 3 | 7 | 3 → 10 → 5 → 16 → 8 → 4 → 2 → 1 |
| 6 | 8 | 6 → 3 → 10 → 5 → 16 → 8 → 4 → 2 → 1 |

### Translating to code

```
If n == 1:           return 0             ← base case
If n is even:        return 1 + collatz(n / 2)    ← recursive case 1
If n is odd:         return 1 + collatz(3n + 1)   ← recursive case 2
```

The `1 +` counts the current step, then adds however many steps the next value takes.

### Solution

```c
int collatz(int n)
{
    if (n == 1)
        return 0;
    else if (n % 2 == 0)
        return 1 + collatz(n / 2);
    else
        return 1 + collatz(3 * n + 1);
}
```

#### Breaking it down

| Line | Role | Explanation |
|------|------|-------------|
| `if (n == 1) return 0;` | **Base case** | At 1 — no more steps needed |
| `n % 2 == 0` | Even check | If remainder when dividing by 2 is 0, n is even |
| `return 1 + collatz(n / 2)` | **Recursive case 1** | 1 step taken, then recurse on half of n |
| `return 1 + collatz(3 * n + 1)` | **Recursive case 2** | 1 step taken, then recurse on 3n+1 |

Verification for n=3:
```
collatz(3)  → odd  → 1 + collatz(10)
collatz(10) → even → 1 + collatz(5)
collatz(5)  → odd  → 1 + collatz(16)
collatz(16) → even → 1 + collatz(8)
collatz(8)  → even → 1 + collatz(4)
collatz(4)  → even → 1 + collatz(2)
collatz(2)  → even → 1 + collatz(1)
collatz(1)  → BASE CASE → 0

Unwinding: 0+1+1+1+1+1+1+1 = 7 steps ✅
```

---

## 8. 📌 Summary

### Recursion defined

> A function that calls itself as part of its execution, with the problem getting smaller each call until a base case is reached.

### The two required parts

| Part | Definition | In `fact(n)` | In `fib(n)` |
|------|-----------|-------------|------------|
| **Base case(s)** | Simplest case — solved directly, no further recursion | `if (n == 1) return 1` | `n==1 → 0`, `n==2 → 1` |
| **Recursive case(s)** | Calls itself with smaller/simpler input | `return n * fact(n-1)` | `return fib(n-1) + fib(n-2)` |

### Types of recursive structures

| Structure | Example | When to use |
|-----------|---------|-------------|
| 1 base case, 1 recursive case | Factorial | Simple linear recursion |
| Multiple base cases | Fibonacci | When the recursive case reaches more than one stopping point |
| Multiple recursive cases | Collatz | When there are two or more valid next steps depending on conditions |

### Recursive vs. iterative

| | Recursive | Iterative |
|-|-----------|----------|
| Code length | Often shorter | Often longer |
| Readability | Mirrors mathematical definitions | More explicit/mechanical |
| Stack usage | Uses call stack for each call | Single stack frame |
| Early termination | Stops at base case | Stops at loop condition |
| Best for | Divide-and-conquer, tree structures, merge sort | Simple loops where iteration is natural |

### Why recursion matters beyond this video

Recursion is not just a stylistic choice — it is the key to algorithms that are otherwise hard to express cleanly. **Merge sort** (the next video) sorts an array by recursively sorting its left half, sorting its right half, and merging them. It achieves O(n log n) — far better than any of the O(n²) algorithms — and recursion is what makes that divide-and-conquer structure possible.
