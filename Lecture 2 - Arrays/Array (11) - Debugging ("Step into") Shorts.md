📋 Debugging with debug50 — "Step Into" (Short by Carter @ CS50)

This short demonstrates debug50's **Step Into** feature. While Step Over treats a function call as a single black-box step, Step Into lets you dive *inside* that function and trace its execution line by line. This is essential when the bug is hidden inside a helper function and Step Over alone can't show you what went wrong.

---

## 1️⃣ The Problem: `secret.c`

### What the program is supposed to do
A password-protected program. Run `./secret`, type the correct password, and it prints "come on in". A wrong password produces no output.

### The correct password
`please`

### The bug
Even when `please` is entered correctly, the program produces no output — "come on in" never appears.

```
$ ./secret
Secret phrase: please
(nothing — silence)

$ ./secret
Secret phrase: let me in
(nothing — expected, this is wrong password)
```

Both correct and incorrect inputs produce the same result: nothing. The program is broken.

---

## 2️⃣ First Attempt: Step Over

Carter sets a breakpoint at the start of `main` and runs:
```
debug50 secret
```

### Stepping through with Step Over

**Step Over `get_string`:**
→ Terminal prompts "Secret phrase:". Type `please`, hit Enter.
```
phrase   = "please"
correct  = false
```

**Step Over `check_phrase(phrase)`:**
→ The function runs... but when it returns:
```
phrase   = "please"
correct  = false    ← still false! Even though the phrase is right.
```

**Step Over `if (correct)` check:**
→ `correct` is `false` → the `if` body is skipped → "come on in" is never printed.

### The problem with Step Over
Step Over treated `check_phrase` as a single atomic step — a black box. We can see the output (`correct = false`), but we can't see *what went wrong inside* `check_phrase`. That's where Step Into comes in.

---

## 3️⃣ Step Into: Diving Inside `check_phrase`

Restart debug50, run it again, enter `please` when prompted — but this time, when execution reaches the `check_phrase(phrase)` call, use **Step Into** instead of Step Over.

This drops the debugger inside the `check_phrase` function so you can trace it line by line.

### Inside `check_phrase`
```c
bool check_phrase(string phrase)
{
    string password = "please";        // line 22
    if (phrase == password)            // line 27
    {
        return true;
    }
    return false;
}
```

**Step Over line 22:** `password = "please"`
```
password = "please"
phrase   = "please"
```

**Step Over line 27:** `if (phrase == password)`

Expected: the condition is true (both are "please") → should jump into the body → `return true`.

**Actual behavior:** Execution jumps to `return false` — line 29. The `if` condition evaluated to **false** even though both strings contain "please".

The bug is on line 27.

---

## 4️⃣ The Bug: Comparing Strings with `==`

```c
if (phrase == password)    // BUG
```

### Why `==` doesn't work for strings in C

In C, a string is not a primitive value like an `int`. Strings are stored as **arrays of characters** somewhere in memory. A string variable doesn't hold the characters directly — it holds the **memory address** of where those characters live.

When you write `phrase == password`, C compares:
- The **memory address** of `phrase`'s characters
- The **memory address** of `password`'s characters

These are two different locations in memory — even if both locations contain the characters `p`, `l`, `e`, `a`, `s`, `e`, `\0`. The addresses are different, so `==` returns `false`.

| What you meant | What C actually does |
|----------------|----------------------|
| "Do these two strings contain the same characters?" | "Are these two strings stored at the exact same memory address?" |
| Almost always what you want | Almost always **not** what you want |

- **Clarification:** This is one of the most common beginner mistakes in C. `==` works perfectly for `int`, `char`, `float`, `bool` — any primitive type. But for strings (`char *` / `string`), you must use `strcmp`.

---

## 5️⃣ The Fix: `strcmp` (String Compare)

To compare the **contents** of two strings, use **`strcmp`** from `<string.h>`.

### How `strcmp` works
```c
strcmp(string1, string2)
```

| Return value | Meaning |
|-------------|---------|
| `0` | The strings are **identical** (same characters in same order) |
| Negative | `string1` comes **before** `string2` alphabetically |
| Positive | `string1` comes **after** `string2` alphabetically |

For checking equality, only the `0` case matters.

### Fixed `check_phrase`
```c
#include <string.h>   // required for strcmp

bool check_phrase(string phrase)
{
    string password = "please";
    if (strcmp(phrase, password) == 0)   // compare contents, not addresses
    {
        return true;
    }
    return false;
}
```

### Step-by-step trace after fix (input: `please`)
| Step | Code | Result |
|------|------|--------|
| 1 | `password = "please"` | password holds `"please"` |
| 2 | `strcmp(phrase, password)` | Compares `"please"` vs `"please"` character by character |
| 3 | All characters match | `strcmp` returns `0` |
| 4 | `0 == 0` → `true` | `if` body runs |
| 5 | `return true` | `correct` becomes `true` in `main` |
| 6 | `if (correct)` → true | "come on in" is printed |

Output: `come on in` ✅

### Test
```
$ make secret
$ ./secret
Secret phrase: please
come on in

$ ./secret
Secret phrase: wrong
(nothing — correct behavior for wrong password)
```

---

## 6️⃣ Step Over vs. Step Into — When to Use Each

| Feature | Step Over | Step Into |
|---------|-----------|-----------|
| What it does | Executes the next line; treats any function call as a single atomic step | Dives inside the next function call to trace it line by line |
| When to use | When you trust helper functions and want to trace the main program flow | When a helper function produces wrong results and you need to see why |
| Analogy | Reading a chapter summary | Reading every paragraph |

### Decision guide
1. Try **Step Over** first — follow the main flow.
2. If a function call produces an unexpected result (wrong variable value after it returns), go back and use **Step Into** on that call.
3. Once you're inside, use Step Over again to trace the function's lines one by one.

---

## 7️⃣ The String Comparison Rule — Critical

| Wrong | Right |
|-------|-------|
| `if (str1 == str2)` | `if (strcmp(str1, str2) == 0)` |
| Compares memory **addresses** | Compares actual **characters** |
| Always false for separately stored strings | Correct way to test string equality |

**Always use `strcmp` to compare strings. Never use `==` on strings in C.**

---

## 8️⃣ Summary

### The debugging workflow with Step Into
| Step | Action |
|------|--------|
| 1 | Observe the bug — wrong output or no output |
| 2 | Set a breakpoint in `main` |
| 3 | Run `debug50 secret` |
| 4 | **Step Over** lines in `main` until you hit a suspicious function call |
| 5 | Use **Step Into** on that function call |
| 6 | **Step Over** lines inside the function — watch variable values |
| 7 | Find the line that produces the wrong result |
| 8 | Fix the code, recompile, retest |

### Key concepts
| Concept | Meaning |
|---------|---------|
| Breakpoint | Pause point in the debugger |
| Step Over | Execute next line; treat function calls as black boxes |
| Step Into | Dive inside the next function call |
| `strcmp(a, b)` | Returns `0` if strings `a` and `b` have identical content |
| `==` on strings | Compares memory addresses — almost always wrong for string comparison |
| String in memory | Stored as an array of `char` ending with `'\0'` — a variable holds the address, not the characters themselves |
