🔄 Recursion (Lecture 3 — Algorithms @ CS50)

Selection sort and bubble sort are both O(n²). Can we do better? The answer is yes — but the technique that unlocks faster sorting is a programming concept we need to understand first: **recursion**. Recursion is one of the most elegant and powerful ideas in all of computer science. Once it clicks, you'll see it everywhere.

---

## 1. 🧠 What Is Recursion?

**Recursion** is when a function calls **itself** as part of its own definition.

Instead of solving a problem all at once, a recursive function:
1. Checks if the problem is small enough to solve directly (the **base case**)
2. If not, solves a **smaller version** of the same problem by calling itself
3. Uses the result of that smaller solution to build the full answer

**Mental model:** Imagine standing between two mirrors facing each other. You see yourself reflected infinitely — but each reflection is slightly smaller. Recursion is like that, except each "reflection" gets smaller until it hits a base case and stops.

---

## 2. 👀 You've Already Seen Recursion — Binary Search

The binary search pseudocode from earlier is inherently recursive. Look at it again:

```
If no doors left
    Return false
If number behind middle door
    Return true
Else if number < middle door
    Search left half
Else if number > middle door
    Search right half
```

The lines `Search left half` and `Search right half` are recursive — they call the **same search process** on a **smaller input**. Binary search is literally defined in terms of itself.

---

## 3. 📞 The Phone Book — Recursion in Week 0

In Week 0, the phone book algorithm was written with `Go back to line 3`:

```
1  Pick up phone book
2  Open to middle of phone book
3  Look at page
4  If person is on page
5      Call person
6  Else if person is earlier in book
7      Open to middle of left half of book
8      Go back to line 3
9  Else if person is later in book
10     Open to middle of right half of book
11     Go back to line 3
12 Else
13     Quit
```

The `Go back to line 3` was a loop. But this could be rewritten more cleanly as:

```
1  Pick up phone book
2  Open to middle of phone book
3  Look at page
4  If person is on page
5      Call person
6  Else if person is earlier in book
7      Search left half of book
9  Else if person is later in book
10     Search right half of book
12 Else
13     Quit
```

`Search left half of book` and `Search right half of book` are now **self-referential** — they describe the same algorithm applied to a smaller portion. This is the essence of recursion.

---

## 4. 🔑 The Two Parts of Every Recursive Function

Every correct recursive function **must** have both of these:

### Base case
The condition that **stops** the recursion. It's the simplest possible version of the problem that can be solved directly — without calling the function again.

- Without a base case → infinite recursion → program crashes (stack overflow)
- The base case is always checked **first**

### Recursive case
The part where the function calls **itself** with a **modified (smaller) input**, moving closer to the base case with each call.

| Part | Purpose | What happens without it |
|------|---------|------------------------|
| Base case | Stops the recursion | Infinite loop → crash |
| Recursive case | Reduces the problem | Never makes progress |

---

## 5. 🏛️ The Pyramid Problem — Iteration vs. Recursion

Consider drawing a pyramid of height 4:
```
#
##
###
####
```

### Version 1 — Iteration (`iteration.c`)

```c
// Draws a pyramid using iteration

#include <cs50.h>
#include <stdio.h>

void draw(int n);

int main(void)
{
    // Get height of pyramid
    int height = get_int("Height: ");

    // Draw pyramid
    draw(height);
}

void draw(int n)
{
    // Draw pyramid of height n
    for (int i = 0; i < n; i++)
    {
        for (int j = 0; j < i + 1; j++)
        {
            printf("#");
        }
        printf("\n");
    }
}
```

#### How the iterative version works

The outer loop runs `n` times (one per row). The inner loop prints `i + 1` hashes per row:

| Outer loop `i` | Inner loop runs `i+1` times | Row printed |
|---------------|----------------------------|-------------|
| 0 | 1 time | `#` |
| 1 | 2 times | `##` |
| 2 | 3 times | `###` |
| 3 | 4 times | `####` |

Straightforward — but let's see the recursive way to think about it.

---

### Version 2 — Recursion (`recursion.c`)

```c
// Draws a pyramid using recursion

#include <cs50.h>
#include <stdio.h>

void draw(int n);

int main(void)
{
    // Get height of pyramid
    int height = get_int("Height: ");

    // Draw pyramid
    draw(height);
}

void draw(int n)
{
    // If nothing to draw
    if (n <= 0)
    {
        return;
    }

    // Draw pyramid of height n - 1
    draw(n - 1);

    // Draw one more row of width n
    for (int i = 0; i < n; i++)
    {
        printf("#");
    }
    printf("\n");
}
```

#### Breaking down the recursive version

```c
if (n <= 0)
{
    return;   // BASE CASE — nothing to draw, stop
}
```
- Base case: if `n` is 0 or negative, there's nothing to draw — return immediately.

```c
draw(n - 1);   // RECURSIVE CASE — draw a smaller pyramid first
```
- Call `draw` with `n - 1`. This draws a pyramid of height `n-1` **before** the current row.

```c
for (int i = 0; i < n; i++)
{
    printf("#");
}
printf("\n");
```
- After the smaller pyramid is fully drawn, print the current row of `n` hashes.

**Key insight:** The recursive call `draw(n-1)` must complete entirely before we print the current row. This is why the rows print in ascending order.

---

## 6. 🔁 Recursive Call Trace — `draw(4)`

Let's trace exactly what happens when `draw(4)` is called, step by step.

```
draw(4) called
  → n=4, not ≤ 0 → proceed
  → calls draw(3)
      → n=3, not ≤ 0 → proceed
      → calls draw(2)
          → n=2, not ≤ 0 → proceed
          → calls draw(1)
              → n=1, not ≤ 0 → proceed
              → calls draw(0)
                  → n=0, IS ≤ 0 → return  ← BASE CASE HIT
              ← back in draw(1): print 1 hash → "#\n"
          ← back in draw(2): print 2 hashes → "##\n"
      ← back in draw(3): print 3 hashes → "###\n"
  ← back in draw(4): print 4 hashes → "####\n"
```

### The call stack visualised

Think of the calls stacking up like a tower — each new call sits on top. When the base case returns, the stack unwinds from the top down:

| Stack level | Function call | State |
|------------|--------------|-------|
| 5 (top) | `draw(0)` | n ≤ 0 → **return** ← base case |
| 4 | `draw(1)` | waiting for draw(0) → then prints `#` |
| 3 | `draw(2)` | waiting for draw(1) → then prints `##` |
| 2 | `draw(3)` | waiting for draw(2) → then prints `###` |
| 1 (bottom) | `draw(4)` | waiting for draw(3) → then prints `####` |

The stack **unwinds** from top to bottom, which is why the output is:
```
#
##
###
####
```

The smallest row prints first because `draw(n-1)` is called **before** the current row is printed.

---

## 7. 🔍 Why Order Matters — A Critical Detail

What would happen if we swapped the recursive call and the print loop?

```c
void draw(int n)
{
    if (n <= 0) return;

    // Print this row FIRST (before recursing)
    for (int i = 0; i < n; i++) printf("#");
    printf("\n");

    draw(n - 1);   // then recurse
}
```

For `draw(4)`, this would output:
```
####
###
##
#
```

The pyramid is **upside down**. The order of the recursive call relative to the print statement determines the output direction. This is a fundamental property of recursion — the execution order is driven by when recursive calls return, not when they're made.

---

## 8. ♾️ What Happens Without a Base Case?

```c
void draw(int n)
{
    // No base case!
    draw(n - 1);
    for (int i = 0; i < n; i++) printf("#");
    printf("\n");
}
```

For `draw(4)`:
```
draw(4) → draw(3) → draw(2) → draw(1) → draw(0) → draw(-1) → draw(-2) → ...
```

The recursion never stops. The call stack fills up with thousands of pending function calls until the system runs out of memory → **stack overflow** → program crashes.

- **Clarification — stack overflow:** Every function call occupies memory on the "call stack." Without a base case, the stack keeps growing indefinitely until the operating system terminates the program. This is always a crash, never a graceful exit.

---

## 9. 📌 Summary

### The two parts — always check for both

| Part | Definition | In `draw(n)` |
|------|-----------|-------------|
| **Base case** | Smallest case — solved directly, no further recursion | `if (n <= 0) return;` |
| **Recursive case** | Calls itself with a smaller/simpler input | `draw(n - 1);` |

### Iteration vs. recursion — pyramid comparison

| | Iteration | Recursion |
|-|-----------|-----------|
| Uses | Nested `for` loops | Function calling itself |
| Rows printed | In a single function body | Each call prints one row after waiting for deeper calls |
| Intuition | "Do this n times" | "Draw a smaller pyramid, then add one row" |
| Output order | Same — ascending rows | Same — ascending rows (because recursion unwinds bottom-up) |

### Rules for writing recursive functions

| Rule | Why it matters |
|------|---------------|
| Always define a base case | Prevents infinite recursion and stack overflow |
| Recursive call must move toward the base case | `draw(n-1)` gets smaller each time — guaranteed to hit n ≤ 0 |
| The call's position determines order | Before → inner calls run first; after → outer call runs first |
| Each call is independent | Each `draw(n)` has its own `n` — the previous call's `n` is unaffected |

### Why recursion matters here

Recursion is not just a different way to write loops. It's the key to expressing **divide and conquer** algorithms naturally — like merge sort, which we cover next. Merge sort splits the array in half, sorts each half (by calling itself), and merges the results. It cannot be expressed as cleanly with iteration alone. Understanding recursion here is the prerequisite for understanding why merge sort achieves O(n log n).
