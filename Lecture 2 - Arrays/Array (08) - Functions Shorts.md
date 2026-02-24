📋 Functions (Short by Doug Lloyd @ CS50)

Functions are the key to writing organized, reusable, and maintainable code. As programs grow from 10 lines to thousands of lines, keeping everything inside `main` becomes unmanageable. Functions let you break a large problem into smaller, solvable pieces.

---

## 1️⃣ What Is a Function?

A function is a **black box** — a self-contained block of code that takes **zero or more inputs** and produces a **single output**.

```
inputs → [black box] → output
```

### Why "black box"?
You don't need to know *how* a function works internally — only *what* it does. The contract is: give it the right inputs and it will produce the expected output.

**Example 1: `add`**
```
a=3, b=6, c=7  →  [add]  →  16
```
Inside the box: adds the three numbers. Outside: you just know 3 + 6 + 7 = 16.

**Example 2: `mult`**
```
a=4, b=5  →  [mult]  →  20
```
Inside the box: could be `a * b`, OR could be adding `a` to itself `b` times — both give 20:

Implementation A (direct multiplication):
```c
output = a * b;
```

Implementation B (repeated addition):
```c
int counter = 0;
// repeat b times: add a to counter
// 0 → +3 → +3 → +3 → +3 → +3  =  15  (for a=3, b=5)
```

Both produce the same result. The implementation doesn't matter to the caller — only the output does. This is the essence of the black box.

- **Clarification:** This is why naming functions clearly is critical. `printf` has been used for 40+ years by millions of programmers who never read its source code — they just trust the name and documentation.

### Synonyms for "function"
You will hear these terms used interchangeably across CS:
| Term | Context |
|------|---------|
| **Function** | C, most languages |
| **Procedure** | Older languages, general CS |
| **Method** | Object-oriented languages (Java, Python) |
| **Subroutine** | Low-level / older programming |

They all refer to the same fundamental idea.

---

## 2️⃣ Why Use Functions?

| Benefit | Explanation |
|---------|-------------|
| **Organization** | Break a huge problem into smaller, manageable sub-problems |
| **Simplification** | Debugging a 10-line function is much easier than a 1,000-line one |
| **Reusability** | Write once, use anywhere — `printf` was written once, used billions of times |

- **Clarification:** Without functions, all code lives inside `main`. As programs grow, this becomes impossible to read, debug, or maintain. Functions are how professional programmers structure large programs of hundreds, thousands, or tens of thousands of lines.

---

## 3️⃣ Function Declarations (Prototypes)

Before you can use a function, you must **declare** it — tell the compiler: "I'm going to write this function later; here's what it looks like."

### Syntax
```
return_type name(argument_list);
```

Three parts:
| Part | Meaning | Example |
|------|---------|---------|
| `return_type` | The data type of the output | `int`, `float`, `bool`, `void` |
| `name` | What you call the function | `add_two_ints`, `strlen`, `printf` |
| `argument_list` | Comma-separated inputs — each with type AND name | `int a, int b` |

### Where to put declarations
Always at the **very top** of your file, before `main`. This lets the compiler know the function exists before it encounters a call to it.

### Example: `add_two_ints`
```c
int add_two_ints(int a, int b);
```
- Return type: `int` (the sum of two integers is an integer)
- Name: `add_two_ints` — self-documenting
- Arguments: two `int` variables called `a` and `b`

- **Clarification:** The argument names in the declaration (`a`, `b`) are optional — the compiler only cares about the types. But including names makes the declaration more readable to humans.

---

## 4️⃣ Exercise: Write a Function Declaration

**Question:** How would you write a function declaration for a function that multiplies two floating-point numbers?

Think about:
- What is the return type? (what does multiplying two floats produce?)
- What is a meaningful name?
- What are the inputs and their types?

**Answer:**
```c
float mult_two_reals(float x, float y);
```

Or equivalently, using `double` (more precise floating-point):
```c
double mult_two_reals(double x, double y);
```

### Step-by-step reasoning
| Part | Decision | Why |
|------|----------|-----|
| Return type | `float` | Multiplying a float by a float gives a float |
| Name | `mult_two_reals` | "multiply two real numbers" — clear, descriptive |
| Arguments | `float x, float y` | Two real number inputs |

Both declarations are valid. `double` gives more decimal precision than `float`. Either is correct here.

---

## 5️⃣ Function Definitions

A **declaration** tells the compiler what the function looks like. A **definition** actually implements it — fills in the black box.

### Declaration vs. Definition side by side
```c
// DECLARATION (semicolon at the end — no body)
float mult_two_reals(float x, float y);

// DEFINITION (open curly brace → body → closed curly brace)
float mult_two_reals(float x, float y)
{
    float product = x * y;
    return product;
}
```

The keyword `return` sends the output back to the caller. Whatever value follows `return` is the output of the black box.

### Shorthand — skip the intermediate variable
```c
float mult_two_reals(float x, float y)
{
    return x * y;   // x and y are floats, so x * y is also a float
}
```

Both implementations are correct. The shorter one is preferred when there's no reason to name an intermediate value.

---

## 6️⃣ Exercise: Define `add_two_ints`

**Question:** Given the declaration below, fill in the definition so the function returns the sum of `a` and `b`.

```c
int add_two_ints(int a, int b)
{
    // your code here
}
```

**Answer — Solution A (intermediate variable):**
```c
int add_two_ints(int a, int b)
{
    int sum = a + b;   // compute sum
    return sum;        // return it
}
```

**Answer — Solution B (direct return):**
```c
int add_two_ints(int a, int b)
{
    return a + b;
}
```

**Answer — Solution C (bad style, but technically works — "bad_adder.c"):**
```c
int add_two_ints(int a, int b)
{
    int sum = 0;
    for (int i = 0; i < b; i++)
    {
        sum += a;          // adds a to sum, b times (repeated addition)
    }
    return sum;
}
```

### Step-by-step for Solution C (a=3, b=5)
| Iteration | `i` | `sum` after `sum += a` |
|-----------|-----|------------------------|
| Start | — | `0` |
| 1st | 0 | `0 + 3 = 3` |
| 2nd | 1 | `3 + 3 = 6` |
| 3rd | 2 | `6 + 3 = 9` |
| 4th | 3 | `9 + 3 = 12` |
| 5th | 4 | `12 + 3 = 15` |

Output: `15` ✅ (3 × 5 = 15 — correct, but terrible approach for addition)

- **Why Solutions A and B are better:** The `+` operator does addition directly. Using a loop for addition is like writing a book with every letter hand-carved in stone — it works, but it's absurdly roundabout. Solution C is only shown to prove the black box principle: the implementation doesn't matter as long as the output is correct.

---

## 7️⃣ Calling Functions — Full Program: `adder1.c`

Once declared and defined, calling a function is straightforward: pass it the right arguments and capture the return value.

### Full program
```c
#include <cs50.h>
#include <stdio.h>

int add_two_ints(int a, int b);   // declaration (prototype)

int main(void)
{
    int x = get_int("Give me an integer: ");
    int y = get_int("Give me another integer: ");
    int z = add_two_ints(x, y);   // function call
    printf("The sum of %i and %i is %i.\n", x, y, z);
}

int add_two_ints(int a, int b)    // definition
{
    int sum = a + b;
    return sum;
}
```

### Step-by-step walkthrough

**Example run 1: inputs `10` and `20`**
| Step | Code | Variables |
|------|------|-----------|
| 1 | `get_int("Give me an integer: ")` → user types `10` | `x = 10` |
| 2 | `get_int("Give me another integer: ")` → user types `20` | `y = 20` |
| 3 | `add_two_ints(x, y)` called — `a=10`, `b=20` | inside function |
| 4 | `sum = 10 + 20` | `sum = 30` |
| 5 | `return sum` → `30` returned | back in `main`, `z = 30` |
| 6 | `printf` formats and prints | — |

Output: `The sum of 10 and 20 is 30.`

**Example run 2: inputs `-10` and `17`**
| Step | Code | Variables |
|------|------|-----------|
| 1 | User types `-10` | `x = -10` |
| 2 | User types `17` | `y = 17` |
| 3 | `add_two_ints(-10, 17)` — `a=-10`, `b=17` | inside function |
| 4 | `sum = -10 + 17` | `sum = 7` |
| 5 | `return 7` | `z = 7` |
| 6 | `printf` | — |

Output: `The sum of -10 and 17 is 7.`

### Key design points
- The function declaration goes **before** `main` — the compiler sees it first.
- The function definition goes **after** `main` — humans see the program flow first.
- `x` and `y` in `main` can be any integers. The function handles them all.
- The return value of `add_two_ints` is captured in `z` and used in `printf`.

---

## 8️⃣ `void` — Functions with No Input or No Output

Sometimes a function doesn't need inputs, or doesn't produce an output.

### `void` return type — function has no output
```c
void print_hello(void)
{
    printf("Hello!\n");
    // no return statement needed
}
```
Used when the function performs an action but doesn't hand back a value.

### `void` argument list — function takes no input
```c
int main(void)
```
You've been writing this since Week 1. `void` in the argument position means "this function takes no inputs." `main` is the most common function with a void argument list.

### Both void
```c
void do_something(void)
{
    // does something, takes nothing, returns nothing
}
```

- **Clarification:** If a function's return type is `void`, you cannot write `return 5;` in it. You can write a bare `return;` to exit early, but you do not return a value.

---

## 9️⃣ Practice Problem: `valid_triangle`

**Goal:** Write a function called `valid_triangle` that:
- Takes three real numbers representing the side lengths of a triangle
- Returns `true` if those three sides can form a valid triangle, `false` otherwise

### Rules for a valid triangle
1. All sides must have **positive** length (no zero or negative sides)
2. The **sum of any two sides** must be **greater than** the third side
   - e.g., sides 1, 2, 4 → invalid because 1 + 2 = 3, which is NOT greater than 4

### Step 1: Write the declaration
```c
bool valid_triangle(float x, float y, float z);
```
- Return type: `bool` (true or false)
- Name: `valid_triangle` — describes exactly what it checks
- Arguments: three `float` values (real numbers for side lengths)

### Step 2: Write the definition
```c
bool valid_triangle(float x, float y, float z)
{
    // Rule 1: all sides must be positive
    if (x <= 0 || y <= 0 || z <= 0)
    {
        return false;
    }

    // Rule 2: sum of any two sides must exceed the third
    if (x + y <= z || x + z <= y || y + z <= x)
    {
        return false;
    }

    // Passed both checks — it's a valid triangle
    return true;
}
```

### Step-by-step explanation

**Rule 1 check:**
```c
if (x <= 0 || y <= 0 || z <= 0)
```
- `x <= 0` catches zero AND negative lengths (both are impossible side lengths)
- `||` (OR): if ANY one side fails the test, we reject immediately
- Immediately `return false` — no point checking Rule 2 if a side is invalid

**Rule 2 check:**
```c
if (x + y <= z || x + z <= y || y + z <= x)
```
- Checks all three pair combinations: (x+y) vs z, (x+z) vs y, (y+z) vs x
- Uses `<=` (not just `<`) because if the sum equals the third side, the three points are collinear — that's a flat line, not a triangle

**Final `return true`:**
- Only reached if both checks above passed
- Guard-clause pattern: handle failures first, happy path at the end

### Test cases
| Sides | Rule 1 — all positive? | Rule 2 — pair sums > third? | Result |
|-------|------------------------|------------------------------|--------|
| 3, 4, 5 | ✅ all > 0 | ✅ 3+4=7>5, 3+5=8>4, 4+5=9>3 | `true` |
| 1, 2, 4 | ✅ all > 0 | ❌ 1+2=3, not > 4 | `false` |
| -1, 5, 5 | ❌ -1 ≤ 0 | (not checked — early return) | `false` |
| 5, 5, 5 | ✅ all > 0 | ✅ 5+5=10 > 5 | `true` |
| 0, 3, 4 | ❌ 0 ≤ 0 | (not checked — early return) | `false` |

### Design notes
- **Guard clauses first:** Invalid cases are rejected immediately with `return false`. The final `return true` is the happy path — clean and readable.
- **Why `<= 0` and not `< 0`:** A side of length 0 makes no geometric sense — it's not a real side.
- **Why `<= z` and not `< z`:** Equal sum means a degenerate (flat) triangle — technically not a triangle.

---

## 🔟 Summary

### Key vocabulary
| Term | Meaning |
|------|---------|
| Function | A black box: zero or more inputs → single output |
| Declaration (prototype) | Tells the compiler the function exists: `type name(args);` |
| Definition | Implements the function body: `type name(args) { ... }` |
| Call | Using the function: `z = add_two_ints(x, y);` |
| Return type | Data type of the output (`int`, `float`, `bool`, `void`) |
| Argument | Input variable passed into a function |
| `void` | "Nothing" — used when there is no input or no output |

### Function declaration syntax
```
return_type function_name(type param1, type param2, ...);
```

### Rules
1. Declarations go **before** `main` (at the top of the file).
2. Definitions go **after** `main` (humans read `main` first).
3. Return type must match what the function actually `return`s.
4. Argument types must match what the caller passes in.
5. Give functions **clear, meaningful names** — they act as documentation for anyone reading your code.
6. Functions that don't output use `void` as their return type.
7. Functions that take no input use `void` in the argument list.
