📋 Variables and Scope (Short by Doug Lloyd @ CS50)

As you start writing functions, a new concept becomes critical: **scope**. Scope determines which parts of your program can "see" and use which variables. Getting this wrong causes subtle bugs that are hard to track down.

---

## 1️⃣ What Is Scope?

**Scope** is a characteristic of a variable that defines from which functions that variable can be accessed.

There are two types of scope in C:
| Type | Where declared | Who can access it |
|------|---------------|------------------|
| **Local** | Inside a function | Only that function |
| **Global** | Outside all functions | Every function in the program |

---

## 2️⃣ Local Variables

A **local variable** is created inside a function. Only that function can use it — no other function can reach in and access it.

```c
int triple(int x)
{
    return x * 3;   // x is LOCAL to triple — only triple can see x
}

int main(void)
{
    int result = triple(5);   // result is LOCAL to main — only main can see result
    printf("%i\n", result);
}
```

- `x` inside `triple` — only `triple` can access it. `main` has no idea `x` exists.
- `result` inside `main` — only `main` can access it. `triple` cannot use `result`.

**Clarification:** This is intentional. Functions are self-contained black boxes. They receive their inputs through parameters, do their work, and send back output. They don't need to reach into another function's variables — that would create chaotic, unmanageable code.

---

## 3️⃣ Global Variables

A **global variable** is declared **outside all functions**. Every function in the program can read and modify it.

```c
float global = 0.5050;   // GLOBAL — declared outside all functions

void triple(void)
{
    global = global * 3;   // modifies the shared global variable
}

int main(void)
{
    triple();
    printf("%f\n", global);   // prints 1.515, not 0.5050
}
```

### Execution trace
| Step | What happens | `global` value |
|------|-------------|----------------|
| Program starts | `global` initialized | `0.5050` |
| `main` calls `triple()` | `triple` multiplies `global` by 3 | → `1.515` |
| Back in `main`, `printf` | Reads `global` | `1.515` |

Output: `1.515`

The change made by `triple` persists — `main` never sees the original `0.5050` again.

### The danger of global variables
- Any function can modify a global variable at any time.
- If function A changes a global, and function B later reads it expecting the original value — that's a bug.
- As programs grow larger with more functions, it becomes impossible to track who changed a global and when.
- **Rule of thumb:** Avoid global variables unless absolutely necessary. Prefer passing values as arguments and returning results.

---

## 4️⃣ Passed by Value

When you pass a local variable to a function, C passes a **copy** of that variable — not the variable itself. This is called **pass by value**.

```c
int triple(int x)
{
    return x * 3;
}

int main(void)
{
    int foo = 4;
    triple(foo);           // foo is STILL 4 after this call!
    printf("%i\n", foo);   // prints 4, not 12
}
```

### Why doesn't `foo` change?

When `triple(foo)` is called:
1. C makes a **copy** of `foo` (a copy of the value `4`).
2. `triple` receives that copy — it doesn't know about `main`'s `foo`.
3. `triple` triples its own copy → `12`.
4. `triple` returns `12`, but since no one captures the return value, the `12` is immediately discarded.
5. `main`'s `foo` was never touched — it's still `4`.

| Step | Code | `foo` in `main` | copy inside `triple` |
|------|------|-----------------|----------------------|
| 1 | `int foo = 4` | `4` | — |
| 2 | `triple(foo)` called | `4` (unchanged) | `4` (copy created) |
| 3 | Inside `triple`: `return x * 3` | `4` (unchanged) | `12` (copy tripled) |
| 4 | `triple` returns — copy discarded | `4` (still unchanged) | — (gone) |
| 5 | `printf("%i\n", foo)` | `4` | — |

Output: `4`

**Analogy:** You hand someone a photocopy of a document. They write notes on the copy. Your original stays blank.

---

## 5️⃣ How to Actually Change a Caller's Variable

If you want `main`'s variable to reflect the result of a function call, you must:
1. Have the function **return** the new value.
2. **Reassign** the variable in `main` with the return value.

```c
int triple(int x)
{
    return x * 3;
}

int main(void)
{
    int foo = 4;
    foo = triple(foo);     // capture the return value and reassign
    printf("%i\n", foo);   // now prints 12
}
```

### Step-by-step
| Step | Code | `foo` in `main` |
|------|------|-----------------|
| 1 | `int foo = 4` | `4` |
| 2 | `triple(foo)` called — copy of `4` passed | `4` (unchanged during call) |
| 3 | Inside `triple`: `return 4 * 3` → returns `12` | `4` (still unchanged) |
| 4 | `foo = triple(foo)` — return value `12` assigned back | **`12`** |
| 5 | `printf("%i\n", foo)` | `12` |

Output: `12`

**Key rule:** A function can only affect the caller's variable if it returns the new value AND the caller stores that return value: `foo = triple(foo);`

---

## 6️⃣ Quiz: What Gets Printed?

This is the tricky example Doug Lloyd walks through. Read the program carefully, then figure out: **what gets printed?**

```c
int increment(int x);   // declaration

int main(void)
{
    int x = 1;
    int y = increment(x);
    printf("x = %i, y = %i\n", x, y);
}

int increment(int x)    // definition
{
    x++;        // increments increment's own local copy of x
    return x;   // returns the incremented copy
}
```

**Answer: `x = 1, y = 2`**

### Why? — Step-by-step trace

To avoid confusion, think of `main`'s `x` as `x_main` and `increment`'s `x` as `x_inc`:

| Step | Code | `x_main` | `x_inc` | `y` |
|------|------|----------|---------|-----|
| 1 | `int x = 1` (in main) | `1` | — | — |
| 2 | `increment(x)` called — a **copy** of `x_main` is passed | `1` | `1` (copy) | — |
| 3 | `x++` inside `increment` increments the copy | `1` **unchanged!** | `2` | — |
| 4 | `return x` — returns `2` | `1` | (function done) | — |
| 5 | `y = increment(x)` — return value stored in `y` | `1` | — | `2` |
| 6 | `printf("x = %i, y = %i\n", x, y)` runs | — | — | — |

Output: `x = 1, y = 2`

### What does this prove?
- `x_main` stays at `1` because `increment` only received a **copy** of it.
- `y` correctly captures the return value `2`.
- Both variables are named `x` — but they live in different functions and are completely separate. The compiler tracks them by scope, not by name alone.

**Clarification:** Having two variables with the same name in different functions is perfectly legal. The compiler knows which one you mean based on where you are in the code. This is exactly why understanding scope is important — name reuse is extremely common (especially with `i`, `x`, `n`, `temp`), and getting confused about which copy you're looking at is a real source of bugs.

---

## 7️⃣ Summary

### Scope types
| Type | Declared | Accessible by | Recommendation |
|------|----------|---------------|----------------|
| Local | Inside a function | Only that function | **Preferred** — safe, predictable |
| Global | Outside all functions | Any function | **Avoid** unless necessary — risky |

### Pass by value
| Concept | Meaning |
|---------|---------|
| Pass by value | The called function receives a **copy** of the variable |
| Original unchanged | The caller's variable is NOT modified by the function |
| To update caller's variable | Function must **return** the new value; caller must **reassign** it |

### Rules
1. Local variables are scoped to the function they're declared in — invisible everywhere else.
2. Global variables are accessible by every function — any function can change them unexpectedly.
3. C passes local variables **by value** — functions work on copies, not originals.
4. To update a caller's variable: return the new value and reassign → `foo = triple(foo);`
5. Two variables can share the same name across different functions — they're completely separate.
6. When in doubt about which variable is being changed: trace scope carefully, or rename for clarity.
