📋 Section 3 — Structs, Recursion & Sorting Algorithms (by Kelly @ CS50)

This section reinforces Week 3 concepts through hands-on exercises and live coding: building custom data types with structs, writing recursive functions for factorials and Fibonacci, and reviewing the three sorting algorithms with their runtime analysis.

---

## 1. 🏗️ Structs — Grouping Related Data

### The core question
How can we group different types of related data into a single, reusable unit?

C's built-in types (`int`, `string`, `float`) each hold one value of one type. But real-world data is more complex — a candidate in an election has both a **name** (string) and a **vote count** (integer). A struct lets you bundle these together into a new custom type.

### The `candidate` struct

```c
typedef struct
{
    string name;
    int votes;
} candidate;
```

| Part | Meaning |
|------|---------|
| `typedef struct` | "I'm defining a new custom type" |
| `{ string name; int votes; }` | The fields — every `candidate` has both |
| `candidate` | The name of the new type — use it like `int` or `string` |

### Creating and using an instance

```c
candidate president;
president.name  = "Alice";
president.votes = 10;
```

- `president` is a **variable** of type `candidate` — just like `int x` is a variable of type `int`
- The **dot (`.`)** accesses a specific field: `president.name` reads/writes the name field

---

## 2. 💻 Exercise 1 — `struct.c` (Hard-coded Candidate)

### Goal
Create a `candidate` struct, assign Alice 5 votes, and print it.

### Final code

```c
#include <cs50.h>
#include <stdio.h>

typedef struct
{
    string name;
    int votes;
} candidate;

int main(void)
{
    candidate a;
    a.name  = "Alice";
    a.votes = 5;
    printf("%s has %i votes.\n", a.name, a.votes);
}
```

### Step-by-step walkthrough

| Step | Code | What happens |
|------|------|-------------|
| 1 | `candidate a;` | Creates a variable `a` of type `candidate` — fields are uninitialized |
| 2 | `a.name = "Alice"` | Sets the `name` field of `a` to `"Alice"` |
| 3 | `a.votes = 5` | Sets the `votes` field of `a` to `5` |
| 4 | `printf(...)` | Prints: `Alice has 5 votes.` |

Output: `Alice has 5 votes.`

### Important: struct goes **before** `main`
```c
typedef struct { ... } candidate;   // ← ABOVE main

int main(void)                      // ← main after
{
    ...
}
```
The compiler reads top-to-bottom. If `candidate` is defined after `main`, the compiler doesn't know what `candidate` means when it sees `candidate a;` — compilation error.

---

## 3. 🔧 Exercise 2 — `get_candidate()` Function (User Input)

### Goal
Instead of hard-coding Alice's data, write a function `get_candidate` that:
- Prompts the user for a name and vote count
- Returns a `candidate` with those values

### Step 1 — What is the return type?

Kelly asks: "What should this function return?"

Answer: **`candidate`** — a variable of our custom type.

```c
candidate get_candidate(void);   // prototype — return type is candidate
```

This is the same pattern as `int add_two_ints(int a, int b)` — the return type just happens to be our custom type instead of `int`.

### Step 2 — First draft (with unnecessary variables)

```c
candidate get_candidate(void)
{
    string name  = get_string("Name: ");
    int votes    = get_int("Votes: ");

    candidate new_candidate;
    new_candidate.name  = name;
    new_candidate.votes = votes;
    return new_candidate;
}
```

Kelly spots a design improvement: the intermediate `name` and `votes` variables aren't needed.

### Step 3 — Refined version (remove unnecessary variables)

```c
candidate get_candidate(void)
{
    // A function to get a new candidate
    candidate new_candidate;
    new_candidate.name  = get_string("Name: ");
    new_candidate.votes = get_int("Votes: ");
    return new_candidate;
}
```

- `get_string` is called directly inside the field assignment — the temporary `name` variable is cut out
- Same for `votes` — saves memory and makes the intent clearer

### The prototype error — a live bug

When Kelly first tried to compile, she got:

```
error: call to undeclared function 'get_candidate'
```

**Cause:** `get_candidate` was defined *after* `main`, but used *inside* `main`. The compiler hit the call before seeing the definition.

**Fix:** Add the **prototype** (function declaration) above `main`:

```c
candidate get_candidate(void);   // ← prototype goes here, before main

int main(void)
{
    ...
}

candidate get_candidate(void)    // ← full definition goes here, after main
{
    ...
}
```

- **Clarification:** This is the same prototype pattern from Week 2. The prototype tells the compiler "this function exists and looks like this" — so it doesn't panic when it sees the call in `main`. The definition can appear later.

### Full working program

```c
#include <cs50.h>
#include <stdio.h>

typedef struct
{
    string name;
    int votes;
} candidate;

candidate get_candidate(void);   // prototype

int main(void)
{
    candidate new_candidate = get_candidate();
    printf("Candidate is named %s and has %i votes.\n",
           new_candidate.name, new_candidate.votes);
}

candidate get_candidate(void)
{
    // A function to get a new candidate
    candidate new_candidate;
    new_candidate.name  = get_string("Name: ");
    new_candidate.votes = get_int("Votes: ");
    return new_candidate;
}
```

### Example run

```
Name: Alice
Votes: 10
Candidate is named Alice and has 10 votes.
```

---

## 4. 📦 Exercise 3 — Array of Candidates

### Goal
Use `get_candidate()` in a loop to fill an array of 3 candidates, then print them all.

### Key insight
An array of structs works just like any other array — each slot holds one complete `candidate` (with both fields inside it):

```
candidates[0] = { name: "Alice", votes: 5  }
candidates[1] = { name: "Bob",   votes: 9  }
candidates[2] = { name: "Charlie", votes: 2 }
```

### Full program

```c
#include <cs50.h>
#include <stdio.h>

typedef struct
{
    string name;
    int votes;
} candidate;

candidate get_candidate(void);

int main(void)
{
    candidate candidates[3];

    // Fill each candidate using the function
    for (int i = 0; i < 3; i++)
    {
        candidates[i] = get_candidate();
    }

    // Print all candidates
    for (int i = 0; i < 3; i++)
    {
        printf("Candidate %i is named %s and has %i votes.\n",
               i + 1, candidates[i].name, candidates[i].votes);
    }
}

candidate get_candidate(void)
{
    // A function to get a new candidate
    candidate new_candidate;
    new_candidate.name  = get_string("Name: ");
    new_candidate.votes = get_int("Votes: ");
    return new_candidate;
}
```

### Step-by-step walkthrough

**Input loop (i = 0, 1, 2):**

| `i` | `get_candidate()` called | Stored at |
|-----|--------------------------|-----------|
| 0 | Prompts for name + votes | `candidates[0]` |
| 1 | Prompts for name + votes | `candidates[1]` |
| 2 | Prompts for name + votes | `candidates[2]` |

**Print loop (i = 0, 1, 2):**

| `i` | `candidates[i].name` | `candidates[i].votes` | `i + 1` | Output |
|-----|--------------------|----------------------|---------|--------|
| 0 | `"Alice"` | 5 | 1 | `Candidate 1 is named Alice and has 5 votes.` |
| 1 | `"Bob"` | 9 | 2 | `Candidate 2 is named Bob and has 9 votes.` |
| 2 | `"Charlie"` | 2 | 3 | `Candidate 3 is named Charlie and has 2 votes.` |

- **Why `i + 1` in the print?** Starting with "Candidate 0" would be confusing to a user. `i + 1` shifts it to "Candidate 1, 2, 3" — zero-indexing is internal, but the user-facing output should start at 1.

### Design discussion: combining the loops

A student asked: can both loops be combined into one?

```c
// Combined version
for (int i = 0; i < 3; i++)
{
    candidates[i] = get_candidate();
    printf("Candidate %i is named %s and has %i votes.\n",
           i + 1, candidates[i].name, candidates[i].votes);
}
```

**The difference:**
| Version | Behaviour |
|---------|-----------|
| Two separate loops | Collects ALL 3 inputs first, THEN prints all 3 |
| One combined loop | Prints each candidate immediately after it's entered |

Both are valid. The choice depends on what the program is supposed to do. Kelly's point: **the programmer decides the intended behaviour** — there's no universally correct answer.

- **Warning — nested loop confusion:** A `for` loop *inside* another `for` loop (both of size 3) would run 3 × 3 = 9 times. Two *sequential* `for` loops of size 3 run 3 + 3 = 6 times. These are very different.

---

## 5. ♾️ Recursion — Solving Problems by Solving Smaller Versions

### Definition

**Recursion** is a technique where a function **calls itself** to solve a smaller version of the same problem.

Every recursive function needs exactly two parts:

| Part | Definition | What happens without it |
|------|-----------|------------------------|
| **Base case** | The simplest case — solved directly without further recursion | Infinite recursion → stack overflow → crash |
| **Recursive case** | The function calls itself with a **smaller** input, moving toward the base case | The function never makes progress |

---

## 6. 🔢 Recursion Example 1 — Factorial

### The math

The **factorial** of a number n (written n!) is the product of all integers from 1 up to n:

```
5! = 5 × 4 × 3 × 2 × 1 = 120
3! = 3 × 2 × 1 = 6
1! = 1
0! = 1  (by definition)
```

### The recursive pattern

The key insight: `n! = n × (n-1)!`

| Factorial | Written recursively |
|-----------|-------------------|
| `3!` | `3 × 2!` |
| `2!` | `2 × 1!` |
| `1!` | `1 × 0!` |
| `0!` | `1` ← base case |

So computing `3!` means:
```
factorial(3)
  = 3 × factorial(2)
  = 3 × 2 × factorial(1)
  = 3 × 2 × 1 × factorial(0)
  = 3 × 2 × 1 × 1
  = 6
```

### The code — `factorial.c`

```c
#include <cs50.h>
#include <stdio.h>

int factorial(int n);   // prototype

int main(void)
{
    int n = get_int("Factorial of what number: ");
    printf("The factorial of %i is %i.\n", n, factorial(n));
}

int factorial(int n)
{
    // Base case
    if (n == 0)
    {
        return 1;
    }
    // Recursive case
    return n * factorial(n - 1);
}
```

### Recursive call trace — `factorial(5)`

```
factorial(5)
  → 5 is not 0 → return 5 * factorial(4)
      factorial(4)
        → 4 is not 0 → return 4 * factorial(3)
            factorial(3)
              → 3 is not 0 → return 3 * factorial(2)
                  factorial(2)
                    → 2 is not 0 → return 2 * factorial(1)
                        factorial(1)
                          → 1 is not 0 → return 1 * factorial(0)
                              factorial(0)
                                → n == 0 → return 1  ← BASE CASE
                          ← returns 1 * 1 = 1
                    ← returns 2 * 1 = 2
              ← returns 3 * 2 = 6
          ← returns 4 * 6 = 24
  ← returns 5 * 24 = 120
```

Output: `The factorial of 5 is 120.`

### Step-by-step unwind table

| Call | Returns | Computed value |
|------|---------|---------------|
| `factorial(0)` | `1` | base case |
| `factorial(1)` | `1 × 1` | `1` |
| `factorial(2)` | `2 × 1` | `2` |
| `factorial(3)` | `3 × 2` | `6` |
| `factorial(4)` | `4 × 6` | `24` |
| `factorial(5)` | `5 × 24` | **`120`** |

### Verified outputs

| Input | `factorial(n)` | Correct? |
|-------|---------------|---------|
| 0 | 1 | ✅ |
| 1 | 1 | ✅ |
| 5 | 120 | ✅ |

### Can you do this with a loop instead?

Yes — any recursive function can be rewritten iteratively (using a loop). These are two different approaches to the same problem:

```c
// Iterative version
int factorial_iter(int n)
{
    int result = 1;
    for (int i = 1; i <= n; i++)
    {
        result *= i;
    }
    return result;
}
```

Both produce identical results. Recursion is often more elegant for naturally self-similar problems; iteration can be more efficient for simple counting tasks.

---

## 7. 🌀 Recursion Example 2 — Fibonacci

### The sequence

The **Fibonacci sequence** is a series where **each element is the sum of the two before it**:

```
Index:  0   1   2   3   4   5   6   7   ...
Value:  0   1   1   2   3   5   8   13  ...
```

- `fib(0)` = 0
- `fib(1)` = 1
- `fib(2)` = 0 + 1 = 1
- `fib(3)` = 1 + 1 = 2
- `fib(4)` = 1 + 2 = 3
- `fib(5)` = 2 + 3 = 5

### The recursive pattern

`fib(n) = fib(n-1) + fib(n-2)`

But this needs **two** base cases — because the recursive case references `n-2`, not just `n-1`:

| Base case | Why needed |
|-----------|-----------|
| `fib(0) = 0` | Defined — the sequence starts at 0 |
| `fib(1) = 1` | Needed because `fib(1)` would otherwise call `fib(-1)`, which doesn't exist |

Without the `n == 1` base case: calling `fib(1)` would try to return `fib(0) + fib(-1)` → `-1` is undefined → infinite recursion.

### The code — `fib.c`

```c
#include <cs50.h>
#include <stdio.h>

int fib(int n);   // prototype

int main(void)
{
    int n = get_int("Which Fibonacci number do you want? ");
    printf("The %ith Fibonacci number is %i.\n", n, fib(n));
}

int fib(int n)
{
    // Base cases
    if (n == 0)
    {
        return 0;
    }
    if (n == 1)
    {
        return 1;
    }
    // Recursive case
    return fib(n - 1) + fib(n - 2);
}
```

### Recursive call trace — `fib(4)`

```
fib(4)
  = fib(3)            + fib(2)
  = (fib(2) + fib(1)) + (fib(1) + fib(0))
  = ((fib(1) + fib(0)) + 1) + (1 + 0)
  = ((1 + 0) + 1) + 1
  = (1 + 1) + 1
  = 2 + 1
  = 3 ✅
```

### Verified outputs

| Input n | `fib(n)` | Sequence check |
|---------|---------|----------------|
| 0 | 0 | ✅ |
| 1 | 1 | ✅ |
| 2 | 1 | ✅ (0+1) |
| 5 | 5 | ✅ (0,1,1,2,3,5) |

- **Important detail:** `fib` makes **two** recursive calls per step. This is very different from `factorial`, which makes only one. This means `fib` builds a **tree** of calls, not a straight chain. The number of calls grows exponentially (which is why Fibonacci computed this way is slow for large n — but it perfectly illustrates the concept).

---

## 8. 🔍 Linear Search — Runtime Review

### The question Kelly posed

"If you had an array of integers but you didn't know what was inside each slot, how would you search for 6?"

- If the array is unsorted → **linear search** (only option)
- If the array is sorted → binary search is also available

### What linear search does

Go through the array one by one, left to right, checking each element:

```
Check slot 1 → not 6
Check slot 2 → not 6
Check slot 3 → not 6
Check slot 4 → found 6! ✅
```

### Runtime

| Scenario | Steps |
|----------|-------|
| Best case | Target is first → 1 check → Ω(1) |
| Worst case | Target is last or absent → n checks → O(n) |

**O(n) — linear growth:** If you double the size of the list, you do approximately double the number of checks. The time scales linearly with input size.

---

## 9. 🔃 Selection Sort — Review

### How it works

Look for the **minimum** value in the unsorted region. Swap it into its correct position at the front. Repeat.

### Pseudocode

```
For i from 0 to n-1:
    Assume element at position i is the smallest
    For j from i+1 to n-1:
        If numbers[j] < numbers[minimum]:
            Update minimum to j
    Swap numbers[i] with numbers[minimum]
```

### Key runtime facts

| | Notation | Why |
|-|----------|-----|
| Worst case | O(n²) | Must scan all remaining elements every pass |
| Best case | **Ω(n²)** | No way to detect sorted early — always does all passes |
| Tight bound | **Θ(n²)** | Best = worst — always quadratic |

The Ω(n²) is the crucial weakness: selection sort **cannot stop early** even if the array is already sorted. It has no mechanism to detect this.

---

## 10. 🫧 Bubble Sort — Review

### How it works

Compare **adjacent pairs** (left, right). If out of order, swap them. The largest unsorted element "bubbles" to the end of the array after each pass. If a full pass produces **zero swaps**, the array is sorted — quit early.

### Pseudocode

```
Repeat n-1 times:
    Set swaps = 0
    For j from 0 to n-2:
        If numbers[j] and numbers[j+1] are out of order:
            Swap them
            Increment swaps
    If swaps == 0:
        Quit
```

- **Why `j` goes to `n-2`:** We compare `numbers[j]` with `numbers[j+1]`. If `j` reached `n-1`, then `j+1 = n` → out of bounds.

### Key runtime facts

| | Notation | Why |
|-|----------|-----|
| Worst case | O(n²) | Array is reverse sorted — maximum swaps every pass |
| Best case | **Ω(n)** | Array already sorted → zero swaps in first pass → quit immediately |
| Tight bound | **None (no Θ)** | Best ≠ worst |

The **Ω(n) is bubble sort's advantage** over selection sort. Because of the `if no swaps → quit` check, a sorted array only requires one pass of n-1 comparisons — linear time.

---

## 11. 🔀 Merge Sort — Review

### How it works (using recursion)

1. Split the array in half
2. Sort the left half (recursive call)
3. Sort the right half (recursive call)
4. Merge the two sorted halves — always take the smaller of the two front elements

### Pseudocode

```
If only one number:
    Quit (base case — already sorted)
Else:
    Sort left half using merge sort
    Sort right half using merge sort
    Merge sorted halves (take smaller front element each time)
```

### The merge step visualised

```
Left:  {3, 5}    Right: {4, 8}

Compare 3 vs 4 → 3 is smaller → take 3
Compare 5 vs 4 → 4 is smaller → take 4
Compare 5 vs 8 → 5 is smaller → take 5
Right has only 8 left → take 8

Result: {3, 4, 5, 8} ✅
```

### Key runtime facts

| | Notation | Why |
|-|----------|-----|
| Worst case | O(n log n) | log n levels of splitting × n work per level |
| Best case | **Ω(n log n)** | Even sorted arrays must be fully split and merged |
| Tight bound | **Θ(n log n)** | Best = worst — always the same |

- **Why it can't detect sorted early:** Like selection sort, merge sort always divides all the way down to single elements and merges back up. There's no shortcut even for an already-sorted array.
- **The trade-off:** Merge sort is significantly faster for large inputs (n log n vs n²), but it requires extra memory for the merge step — it cannot sort purely in-place.

---

## 12. 📊 Final Summary — All Three Sorting Algorithms

| Algorithm | Strategy | Best case Ω | Worst case O | Theta Θ |
|-----------|----------|------------|-------------|---------|
| **Selection sort** | Find minimum, swap to front | Ω(n²) | O(n²) | Θ(n²) |
| **Bubble sort** | Swap adjacent pairs, quit if no swaps | **Ω(n)** | O(n²) | None |
| **Merge sort** | Split, sort recursively, merge | Ω(n log n) | O(n log n) | **Θ(n log n)** |

### Comparison at n = 1,000

| Algorithm | Approximate steps (worst case) |
|-----------|-------------------------------|
| Selection sort | 1,000,000 (n²) |
| Bubble sort | 1,000,000 (n²) |
| Merge sort | ~9,966 (n log n) |

### Key distinctions

| | Selection sort | Bubble sort | Merge sort |
|-|---------------|-------------|------------|
| Detects sorted? | ❌ No | ✅ Yes (Ω(n)) | ❌ No |
| Best and worst same? | ✅ Θ(n²) | ❌ No | ✅ Θ(n log n) |
| Uses recursion? | ❌ | ❌ | ✅ |
| Faster than n²? | ❌ | ❌ | ✅ O(n log n) |

---

## 13. 🔑 Key Concepts — Quick Reference

### Structs
| Concept | Detail |
|---------|--------|
| `typedef struct { ... } Name;` | Define a new custom type |
| `Name var;` | Create an instance |
| `var.field` | Access a field with dot notation |
| `Name arr[n];` | Array of structs |
| `arr[i].field` | Access field of struct at index i |
| Prototype needed | Return type can be a struct — needs prototype before `main` |

### Recursion
| Concept | Detail |
|---------|--------|
| Base case | Stops recursion — must always be checked first |
| Recursive case | Calls itself with smaller input |
| Without base case | Infinite recursion → stack overflow → crash |
| Factorial base case | `n == 0` → return 1 |
| Fibonacci base cases | `n == 0` → return 0; `n == 1` → return 1 (two needed!) |
| Equivalent to | Any recursion can also be written as a loop |
