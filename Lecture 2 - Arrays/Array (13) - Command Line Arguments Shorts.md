📋 Command-Line Arguments (Short by Doug Lloyd @ CS50)

So far, user input has been collected *while the program runs* — via `get_int`, `get_string`, etc. Command-line arguments give users a way to provide data **at launch time**, before the program starts executing. This enables more flexible, scriptable programs that follow Unix conventions.

---

## 1️⃣ The Distinction: Launch-Time vs. Runtime Input

| Method | When input is provided | Example |
|--------|----------------------|---------|
| `get_int` / `get_string` | **While** the program runs (mid-execution prompt) | "How tall? " → user types `5` |
| Command-line arguments | **At launch**, before the program starts | `./mario 5` — `5` is given upfront |

### Why command-line arguments?
- Allows **scripting and automation** — you can chain programs together or write shell scripts
- Follows **Unix conventions** — `make`, `cd`, `gcc` all use command-line arguments
- Saves time for repeat uses — no need to re-answer prompts every run
- Enables programs to be **configured at startup** without being interactive

---

## 2️⃣ New `main` Signature

To accept command-line arguments, change your `main` declaration:

```c
// Before (no arguments):
int main(void)

// After (with command-line arguments):
int main(int argc, string argv[])
```

Two new parameters are added:
| Parameter | Full name | Type | Meaning |
|-----------|-----------|------|---------|
| `argc` | argument count | `int` | How many words the user typed at the command line (including the program name) |
| `argv` | argument vector | `string[]` | An array of those words as strings |

- **Clarification:** `argc` and `argv` are conventional names — you could name them anything (`n` and `words`, for example). But `argc` and `argv` are so universally recognized in C that you should always use them. "Vector" is just another word for "array."

---

## 3️⃣ `argc` — Argument Count

`argc` is an integer that tells you how many words (tokens) the user typed when running the program.

**Crucially:** The **program name itself** counts as one argument. User-supplied arguments come after it.

### Examples
```
./greedy
→ argc = 1          (just the program name — no extra arguments)

./greedy 1024 CS50
→ argc = 3          (program + 2 user arguments)

./caesar 13
→ argc = 2          (program + 1 user argument)
```

Arguments are separated by **whitespace** (spaces or tabs). Each whitespace-separated token increments `argc`.

| Command typed | `argc` value | Why |
|---------------|-------------|-----|
| `./hello` | `1` | Only the program name |
| `./hello world` | `2` | Program + 1 word |
| `./greedy 1024 CS50` | `3` | Program + 2 words |

---

## 4️⃣ `argv` — Argument Vector

`argv` is an **array of strings** — one string per command-line token.

### Structure
| Index | Always contains | When present |
|-------|----------------|-------------|
| `argv[0]` | The program name (e.g., `"./greedy"`) | Always — every run |
| `argv[1]` | First user argument | Only if `argc >= 2` |
| `argv[2]` | Second user argument | Only if `argc >= 3` |
| `argv[argc - 1]` | Last user argument | Always the last element |
| `argv[argc]` | **Out of bounds** | Never access this! |

### Why `argv[argc - 1]` is the last element
If the user typed 3 things (`argc = 3`), the array has elements at indices 0, 1, and 2. Index 3 doesn't exist. Last valid index = `argc - 1` = `3 - 1` = `2`. This follows from standard zero-indexing rules.

---

## 5️⃣ Example: `./greedy 1024 CS50`

```
argc    = 3
argv[0] = "./greedy"
argv[1] = "1024"
argv[2] = "CS50"
```

### Step-by-step breakdown
| Index | Value | Why |
|-------|-------|-----|
| `argv[0]` | `"./greedy"` | Program name — always at index 0 |
| `argv[1]` | `"1024"` | First user-provided argument |
| `argv[2]` | `"CS50"` | Second user-provided argument |
| `argv[3]` | ❌ undefined | `argc = 3` → valid indices are 0, 1, 2 only |

---

## 6️⃣ Critical Rule: Everything in `argv` Is a String

Even when the user types a number like `1024`, it is stored in `argv` as the **string** `"1024"` — not the integer `1024`.

The string `"1024"` is stored in memory as:
```
'1'  '0'  '2'  '4'  '\0'
```

### You CANNOT do math directly on `argv` elements:
```c
int x = argv[1] - 24;    // WRONG — argv[1] is a string, not an integer
```

This would not subtract 24 from 1024 — it would do something involving memory addresses, producing garbage or a crash.

### To use a numeric argument as an integer, convert it:
```c
#include <stdlib.h>

int x = atoi(argv[1]);    // atoi = "ASCII to integer"
                          // converts "1024" → 1024
```

`atoi` is from `<stdlib.h>`. It parses a string of digit characters and returns the integer value. This function (and alternatives) will be introduced in a later problem's walkthrough.

| Input `argv[1]` | `atoi(argv[1])` result |
|----------------|------------------------|
| `"13"` | `13` |
| `"1024"` | `1024` |
| `"0"` | `0` |
| `"-5"` | `-5` |

---

## 7️⃣ Full Example: Validating `argc` Before Using `argv`

A standard pattern: always check `argc` before accessing `argv` elements to avoid out-of-bounds access.

```c
#include <cs50.h>
#include <stdio.h>
#include <stdlib.h>

int main(int argc, string argv[])
{
    // Guard clause: require exactly 2 arguments (program name + 1 user arg)
    if (argc != 2)
    {
        printf("Usage: ./program [argument]\n");
        return 1;
    }

    // Safe to access argv[1] here
    printf("You entered: %s\n", argv[1]);
    return 0;
}
```

### Step-by-step for `./program hello`
| Step | Code | State |
|------|------|-------|
| 1 | Program starts | `argc = 2`, `argv[0] = "./program"`, `argv[1] = "hello"` |
| 2 | `argc != 2` → `2 != 2` → **false** | Guard clause skipped |
| 3 | `printf("You entered: %s\n", argv[1])` | Prints: `You entered: hello` |
| 4 | `return 0` | Program exits successfully |

Output: `You entered: hello`

### Step-by-step for `./program` (no arguments)
| Step | Code | State |
|------|------|-------|
| 1 | Program starts | `argc = 1` — only the program name |
| 2 | `argc != 2` → `1 != 2` → **true** | Guard clause triggers |
| 3 | `printf("Usage: ./program [argument]\n")` | Prints usage instructions |
| 4 | `return 1` | Program exits with error code |

Output: `Usage: ./program [argument]`

### Step-by-step for `./program hello world` (too many arguments)
| Step | Code | State |
|------|------|-------|
| 1 | Program starts | `argc = 3` |
| 2 | `argc != 2` → `3 != 2` → **true** | Guard clause triggers |
| 3 | `printf("Usage: ...")` | Prints usage instructions |
| 4 | `return 1` | Error exit |

Output: `Usage: ./program [argument]`

---

## 8️⃣ Out-of-Bounds Danger

Accessing `argv[argc]` or beyond is out-of-bounds — same risk as going past the end of any array.

```c
// argc = 3 → valid: argv[0], argv[1], argv[2]
string s = argv[3];   // DANGER — segfault risk
```

C compiles this without complaint. At runtime, you're reading memory that doesn't belong to your program. The most likely outcome when working with `argv` specifically: a **segmentation fault**.

**Rule:** Always check `argc` before accessing any `argv` element:
- Need `argv[1]`? Check `argc >= 2` first.
- Need `argv[2]`? Check `argc >= 3` first.
- And so on.

---

## 9️⃣ Summary

### The two parameters
| Parameter | Type | Meaning |
|-----------|------|---------|
| `argc` | `int` | Number of command-line tokens (program name counts as 1) |
| `argv` | `string[]` | Array of those tokens — each is a string |
| `argv[0]` | `string` | Always the program name |
| `argv[argc - 1]` | `string` | Last user-provided argument |

### Rules
1. `argc` always counts from 1 — the program name is always argument 0.
2. `argv[0]` is always the program name — user args start at `argv[1]`.
3. Everything in `argv` is a **string** — even numbers like `"1024"`.
4. Use `atoi()` from `<stdlib.h>` to convert a numeric string argument to an actual integer.
5. Always check `argc` before accessing `argv` elements — out-of-bounds access causes a segfault.
6. `argv[argc]` is always out of bounds — never access it.

### Comparison: input methods
| Method | Signature | When |
|--------|-----------|------|
| No input | `int main(void)` | Program needs no external data |
| In-program prompts | `int main(void)` + `get_int` / `get_string` | Interactive programs |
| Command-line arguments | `int main(int argc, string argv[])` | Data provided at launch |
