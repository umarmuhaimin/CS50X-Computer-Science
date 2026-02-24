📋 Section 2 — Arrays, Strings & Command-Line Arguments (by Kelly@CS50)

This section reinforces Week 2 concepts through interactive exercises: good code design, arrays, strings as character arrays, ASCII arithmetic, and command-line arguments.

---

## 1️⃣ Good Code Design

Three questions to ask yourself every time you write code:

### Is your code efficient?
- **Avoid unnecessary repetition:** if you're copy-pasting code (even with slight changes), that's a sign you should use a **loop** or a **function** instead.
- **Avoid redundant operations:** if a value is computed multiple times but never changes, store it in a variable once and reuse it.

**Example — storing `strlen` in a variable:**

Option A (bad):
```c
for (int i = 0; i < strlen(text); i++)  // strlen called EVERY iteration
```

Option B (good):
```c
int n = strlen(text);
for (int i = 0; i < n; i++)  // strlen called ONCE
```
- Why B is better: `strlen` walks the entire string each call. If the string is 1000 chars and the loop runs 1000 times, Option A does ~1,000,000 character checks. Option B does ~1,000.
- Clarification: the string length doesn't change during the loop, so computing it once is sufficient.

---

### Is your code easy to maintain?

**Prototype your functions:** declare helper functions above `main` so readers see `main` first.
```c
int helper(int x);   // prototype at the top

int main(void)
{
    // main code here — easy to find
}

int helper(int x)
{
    // implementation below
}
```
- Why: humans read top-down. Putting `main` at the top lets someone understand the program flow immediately.

**Avoid magic numbers:** hard-coded numbers whose meaning isn't obvious.
```c
// BAD — what does 26 mean?
for (int i = 0; i < 26; i++)

// GOOD — self-documenting
const int ALPHABET_SIZE = 26;
for (int i = 0; i < ALPHABET_SIZE; i++)
```
- Why: `26` by itself is cryptic. A named constant tells the reader exactly what the number represents.
- Clarification: use `const int` for values that never change during the program. The alphabet won't grow mid-execution.

**Example — months in a year:**

Option A (good):
```c
const int MONTHS_IN_YEAR = 12;
for (int i = 0; i < MONTHS_IN_YEAR; i++)
```

Option B (bad):
```c
for (int i = 0; i < 12; i++)  // what is 12?
```
- Why A is better: someone reading Option B might not immediately know what `12` refers to. The constant makes it obvious.

---

### Is your code readable and not redundant?

**Avoid redundant `else` blocks:**

Option A (good):
```c
if (expression)
{
    return 0;
}
return 1;   // only reached if expression was false
```

Option B (bad):
```c
if (expression)
{
    return 0;
}
else        // redundant — this runs anyway if the if-branch didn't return
{
    return 1;
}
```
- Why A is better: after `return 0`, the function exits. If we reach line after the `if`, we already know the expression was false. The `else` adds no information.
- Clarification: this only applies when the `if` block contains a `return` (or `break`/`continue`). If it doesn't exit, the `else` is needed.

**Use guard clauses (early return):**

Option A (bad):
```c
if (success)
{
    // lots of code...
    // lots of code...
    // lots of code...
}
else
{
    return 1;  // exit on failure
}
```

Option B (good):
```c
if (!success)
{
    return 1;  // guard clause — exit immediately on failure
}
// lots of code...
// lots of code...
// lots of code...
```
- Why B is better: the failure case is handled immediately at the top, then the rest of the function is the "happy path" with no extra nesting.
- Clarification: guard clauses reduce indentation and make it clear what conditions cause early termination.

---

## 2️⃣ Arrays

### Why arrays?
Imagine storing the top 5 Pac-Man scores. Without arrays:
```c
int score1 = 120;
int score2 = 86;
int score3 = 75;
int score4 = 60;
int score5 = 50;
```
- Problem: 5 separate variables. Sloppy, hard to loop over, doesn't scale.

With an array:
```c
int scores[5];
```
- One variable holds all 5 scores. Clean, loopable, scalable.

### Declaring an array
```
type name[size];
```
```c
int scores[5];
//  ^^^         → type: each element is an int
//      ^^^^^^  → name: "scores"
//            ^ → size: 5 elements
```
- Clarification: all elements must be the **same type**. You cannot mix `int` and `string` in one array.
- Important: arrays in C are **static** — once declared with size 5, they stay size 5. You cannot add a 6th element. (Dynamic structures come in later weeks.)

### Zero-indexing
Arrays count from 0, not 1:
```
Index:  [0]   [1]   [2]   [3]   [4]
Value:  120    86    75    60    50
```

### Updating values
One at a time:
```c
scores[0] = 120;   // first element
scores[1] = 86;    // second element
scores[2] = 75;
scores[3] = 60;
scores[4] = 50;
```

All at once (if you know the values upfront):
```c
int scores[5] = {120, 86, 75, 60, 50};
```

### Printing an array with a for loop
```c
int scores[5] = {120, 86, 75, 60, 50};

for (int i = 0; i < 5; i++)
{
    printf("%i\n", scores[i]);
}
```

**Step-by-step execution:**
| Iteration | `i` | `scores[i]` | Output |
|-----------|-----|-------------|--------|
| 1st       | 0   | `scores[0]` = 120 | `120` |
| 2nd       | 1   | `scores[1]` = 86  | `86`  |
| 3rd       | 2   | `scores[2]` = 75  | `75`  |
| 4th       | 3   | `scores[3]` = 60  | `60`  |
| 5th       | 4   | `scores[4]` = 50  | `50`  |

- Why `i < 5` and not `i <= 5`: with zero-indexing, the valid indices are 0–4. `scores[5]` would be out of bounds.

### Key rules for arrays
1. All elements must be the **same data type**.
2. Size is **fixed** at declaration (in C).
3. Indexing starts at **0**.
4. Accessing beyond the size causes **undefined behavior** (no error message — just bugs).

---

## 3️⃣ Strings Are Arrays of Characters

A `string` in C is just an **array of `char`** with a special ending character.

```
string name = "EMMA";
```
Stored in memory as:
```
Index:  [0]  [1]  [2]  [3]  [4]
Value:   'E'  'M'  'M'  'A'  '\0'
```

### The null terminator `\0`
- Every C string ends with `\0` (the **NUL character**, ASCII value 0).
- Why it exists: when the string is stored in memory, `\0` tells the computer "this is where the string ends." Without it, functions like `strlen` or `printf` wouldn't know when to stop reading.
- `"EMMA"` has 4 visible characters but takes **5 bytes** (4 letters + 1 NUL).

### Indexing into strings
Just like arrays, use square brackets:
```c
string name = "EMMA";
printf("%c\n", name[0]);   // prints: E
printf("%c\n", name[1]);   // prints: M
printf("%c\n", name[3]);   // prints: A
```
- Clarification: `%c` is the format specifier for a single character. `%s` prints the whole string.

### `string` is a CS50 training wheel
- `string` is not a native C keyword. It comes from `cs50.h`.
- Under the hood, `string` is actually `char *` (a pointer to a character). You'll learn about pointers in a later week.

---

## 4️⃣ Exercise: print.c — Print String Character by Character

**Goal:** take a string from the user and print each character one by one.

### Final code
```c
#include <cs50.h>
#include <stdio.h>
#include <string.h>

int main(void)
{
    string text = get_string("Input: ");

    for (int i = 0, n = strlen(text); i < n; i++)
    {
        printf("%c", text[i]);
    }
    printf("\n");
}
```

### Step-by-step walkthrough

**Libraries needed:**
| Library    | Why                        |
|------------|----------------------------|
| `cs50.h`   | for `get_string`           |
| `stdio.h`  | for `printf`               |
| `string.h` | for `strlen`               |

**How it works:**
1. `get_string` prompts the user and stores their input in `text`.
2. `strlen(text)` computes the length once, stores it in `n`.
3. The for loop iterates `i` from `0` to `n - 1`.
4. Each iteration prints `text[i]` — one character.
5. After the loop, `printf("\n")` adds a newline.

**Example run with input `"KELLY"`:**

| Iteration | `i` | `text[i]` | Printed |
|-----------|-----|-----------|---------|
| 1st       | 0   | `'K'`     | K       |
| 2nd       | 1   | `'E'`     | E       |
| 3rd       | 2   | `'L'`     | L       |
| 4th       | 3   | `'L'`     | L       |
| 5th       | 4   | `'Y'`     | Y       |

Output: `KELLY`

### Design note
- `n = strlen(text)` is cached in the for loop initializer — good design (avoid recomputing every iteration).
- Using `\n` inside the loop (`printf("%c\n", text[i])`) would print each character on its own line: `K`, `E`, `L`, `L`, `Y` vertically.

---

## 5️⃣ Exercise: reverse.c — Reverse a String

**Goal:** take a string from the user and print it backwards.

### Final code
```c
#include <cs50.h>
#include <stdio.h>
#include <string.h>

int main(void)
{
    string text = get_string("Input: ");
    int n = strlen(text);

    for (int i = n - 1; i >= 0; i--)
    {
        printf("%c", text[i]);
    }
    printf("\n");
}
```

### Step-by-step walkthrough

**Key changes from print.c:**
| Part          | print.c (forward) | reverse.c (backward) |
|---------------|--------------------|-----------------------|
| Start `i` at  | `0`               | `n - 1` (last index) |
| Condition      | `i < n`           | `i >= 0`             |
| Update         | `i++`             | `i--`                |

**Why `n - 1` and not `n`?**
If the string is `"CS50"` (length 4), the valid indices are 0, 1, 2, 3. Starting at `n` (which is 4) would access `text[4]` = `\0` (the null terminator), which is not a visible character.

**Example run with input `"THIS IS CS50"`:**

| Iteration | `i`  | `text[i]` | Printed so far |
|-----------|------|-----------|----------------|
| 1st       | 11   | `'0'`     | 0              |
| 2nd       | 10   | `'5'`     | 05             |
| 3rd       | 9    | `'S'`     | 05S            |
| 4th       | 8    | `'C'`     | 05SC           |
| 5th       | 7    | `' '`     | 05SC           |
| 6th       | 6    | `'S'`     | 05SC S         |
| 7th       | 5    | `'I'`     | 05SC SI        |
| 8th       | 4    | `' '`     | 05SC SI        |
| 9th       | 3    | `'S'`     | 05SC SI S      |
| 10th      | 2    | `'I'`     | 05SC SI SI     |
| 11th      | 1    | `'H'`     | 05SC SI SIH    |
| 12th      | 0    | `'T'`     | 05SC SI SIHT   |

Output: `05SC SI SIHT`

### Common bug: the off-by-one error
Kelly demonstrated this live. Starting at `i = n` instead of `i = n - 1` with condition `i > 0`:
- Missed the first character (`text[0]`) because loop stops when `i` equals 0.
- **Fix:** either start at `n - 1` with `i >= 0`, or start at `n` with `i > 0` and use `text[i - 1]`.

---

## 6️⃣ ASCII Values and Character Arithmetic

Every character has a numeric value in the ASCII table. This lets you do **math on characters**.

### Key ASCII values to remember
| Character | ASCII Value |
|-----------|-------------|
| `'A'`     | 65          |
| `'B'`     | 66          |
| `'Z'`     | 90          |
| `'a'`     | 97          |
| `'b'`     | 98          |
| `'z'`     | 122         |
| `'\0'`    | 0           |

### Why this matters
- Letters are sequential: `'a'` = 97, `'b'` = 98, `'c'` = 99...
- So if `char_a < char_b`, then `char_a` comes **before** `char_b` alphabetically.
- You can do arithmetic: `'A' + 1` = `'B'` (65 + 1 = 66).
- The gap between lowercase and uppercase is always 32: `'a' - 'A'` = 32.

### Example: "EMMA" as integers
```
'E' = 69,  'M' = 77,  'M' = 77,  'A' = 65
```
The string is stored as: `{69, 77, 77, 65, 0}` (the 0 is `\0`).

---

## 7️⃣ Exercise: alphabetical.c — Check Alphabetical Order

**Goal:** given a lowercase string, print `"Yes"` if its characters are in alphabetical order, `"No"` otherwise.

### Final code
```c
#include <cs50.h>
#include <stdio.h>
#include <string.h>

int main(void)
{
    string text = get_string("Input: ");
    int length = strlen(text);

    for (int i = 0; i < length - 1; i++)
    {
        if (text[i] > text[i + 1])
        {
            printf("No\n");
            return 0;
        }
    }
    printf("Yes\n");
}
```

### Step-by-step walkthrough

**Logic:**
1. Loop through each pair of adjacent characters.
2. Compare `text[i]` with `text[i + 1]`.
3. If `text[i] > text[i + 1]` → a later character has a smaller ASCII value → **not alphabetical** → print "No" and exit immediately.
4. If the entire loop completes without finding any out-of-order pair → **alphabetical** → print "Yes".

**Why `i < length - 1` and not `i < length`?**
- We compare `text[i]` with `text[i + 1]`.
- If `i` goes up to `length - 1`, then `i + 1` = `length`, which is the `\0` null terminator (ASCII 0).
- Since `\0` (0) is less than any letter, the comparison would falsely say "not alphabetical."
- Fix: stop one character early with `length - 1`.

**Example run with input `"abc"`:**

| Iteration | `i` | `text[i]` (ASCII) | `text[i+1]` (ASCII) | `text[i] > text[i+1]`? |
|-----------|-----|--------------------|----------------------|------------------------|
| 1st       | 0   | `'a'` (97)         | `'b'` (98)           | 97 > 98? **No** → continue |
| 2nd       | 1   | `'b'` (98)         | `'c'` (99)           | 98 > 99? **No** → continue |

Loop ends → print `"Yes"`

**Example run with input `"cba"`:**

| Iteration | `i` | `text[i]` (ASCII) | `text[i+1]` (ASCII) | `text[i] > text[i+1]`? |
|-----------|-----|--------------------|----------------------|------------------------|
| 1st       | 0   | `'c'` (99)         | `'b'` (98)           | 99 > 98? **Yes** → print "No", exit |

Output: `No`

### Design decisions in this exercise
1. **Guard clause pattern:** instead of checking "is it in order?" and doing nothing, we check "is it OUT of order?" and exit immediately. This avoids an empty `if` body.
2. **Early return:** `return 0` inside the loop stops the program as soon as one out-of-order pair is found. No need to check the rest.
3. **Store `strlen` in a variable:** `int length = strlen(text)` — avoids recomputing length on every loop iteration.

### Debugging technique shown
Kelly added temporary `printf` statements to see the actual values being compared:
```c
printf("%i and %i\n", text[i], text[i + 1]);
```
This revealed the `\0` comparison bug. Once fixed, the debug prints were removed. This is a useful debugging technique: print intermediate values to understand what your code is actually doing.

---

## 8️⃣ Command-Line Arguments

### Why command-line arguments?
Instead of prompting the user mid-program with `get_string`, you can accept input **at launch time**:
```
./caesar 13          ← 13 is a command-line argument
make mario           ← mario is a command-line argument
```
- Why: saves time, allows scripting/automation, follows Unix conventions.

### Syntax: changing `main`
```c
// Before (no arguments):
int main(void)

// After (with command-line arguments):
int main(int argc, string argv[])
```

| Parameter | Type            | Meaning                                    |
|-----------|-----------------|--------------------------------------------|
| `argc`    | `int`           | **Argument count** — how many words were typed |
| `argv`    | `string[]`      | **Argument vector** — array of those words  |

### How `argc` and `argv` work

**Example:** typing `./initials Kelly Ding` in the terminal:
```
argv[0] = "./initials"    (the program name — always present)
argv[1] = "Kelly"
argv[2] = "Ding"
argc = 3
```

**Example:** typing `./caesar 13`:
```
argv[0] = "./caesar"
argv[1] = "13"            (NOTE: this is a STRING "13", not the integer 13)
argc = 2
```

### Key facts
1. `argv[0]` is **always** the program name. User arguments start at `argv[1]`.
2. `argc` counts from **1** (it's a count, not an index). If you type 2 words, `argc` = 2.
3. Everything in `argv` is a **string**, even numbers. `"13"` is the string `{'1', '3', '\0'}`.
4. `argv` is a **2D structure**: an array of strings, where each string is itself an array of characters.

### Accessing individual characters in argv
Since `argv[i]` is a string, and a string is an array of characters, you can double-index:
```
./initials Kelly Ding

argv[1][0] = 'K'    ← first char of "Kelly"
argv[2][0] = 'D'    ← first char of "Ding"
```
- `argv[1]` = the string `"Kelly"`
- `argv[1][0]` = the character `'K'`

---

## 9️⃣ Exercise: initials.c — Print Initials from Command-Line Arguments

**Goal:** run `./initials Kelly Ding` and output `KD`. Accept any number of names.

### Final code
```c
#include <cs50.h>
#include <stdio.h>
#include <string.h>

int main(int argc, string argv[])
{
    // Guard clause: ensure at least one name was provided
    if (argc < 2)
    {
        printf("Usage: ./initials [first name] [last name]\n");
        return 1;
    }

    // Loop through each name argument (skip argv[0] which is the program name)
    for (int i = 1; i < argc; i++)
    {
        printf("%c", argv[i][0]);
    }
    printf("\n");
}
```

### Step-by-step walkthrough

**Example run: `./initials Kelly Ding`**

State before the loop:
```
argc = 3
argv[0] = "./initials"
argv[1] = "Kelly"
argv[2] = "Ding"
```

Guard clause check: `argc < 2`? → `3 < 2`? → **false** → continue.

| Iteration | `i` | `argv[i]`  | `argv[i][0]` | Printed |
|-----------|-----|------------|--------------|---------|
| 1st       | 1   | `"Kelly"`  | `'K'`        | K       |
| 2nd       | 2   | `"Ding"`   | `'D'`        | KD      |

Output: `KD`

**Example run: `./initials David J Malan`**

```
argc = 4
argv[0] = "./initials"
argv[1] = "David"
argv[2] = "J"
argv[3] = "Malan"
```

| Iteration | `i` | `argv[i]`  | `argv[i][0]` | Printed |
|-----------|-----|------------|--------------|---------|
| 1st       | 1   | `"David"`  | `'D'`        | D       |
| 2nd       | 2   | `"J"`      | `'J'`        | DJ      |
| 3rd       | 3   | `"Malan"`  | `'M'`        | DJM     |

Output: `DJM`

**Example run: `./initials` (no name given)**

```
argc = 1
```

Guard clause: `argc < 2`? → `1 < 2`? → **true** → print usage message and `return 1`.

Output: `Usage: ./initials [first name] [last name]`

### Why the loop starts at `i = 1`
- `argv[0]` is always the program name (`"./initials"`). We don't want its first character.
- User-provided names start at `argv[1]`.

### Why use `argc` instead of a hardcoded number
- `argc` adapts to however many arguments the user provides.
- Hardcoding `3` would break for people with middle names (4 args) or single names (2 args).

### Error handling with `return 1`
- `return 0` = program ran successfully.
- `return 1` = program encountered an error (wrong usage in this case).
- Printing a **usage message** is good practice — tells the user how to correctly run the program.

---

## 🔟 Summary — Everything You Need to Know

### Design Principles
| Principle              | Rule of Thumb                                             |
|------------------------|-----------------------------------------------------------|
| Efficiency             | Don't recompute values that don't change (cache `strlen`) |
| No magic numbers       | Use `const int` for hard-coded values                     |
| No redundant code      | Drop unnecessary `else` after `return`                    |
| Guard clauses          | Handle error/exit cases first, then write the happy path  |
| Prototypes             | Declare helper functions above `main`                     |
| Don't repeat yourself  | Use loops and functions instead of copy-paste              |

### Arrays
| Concept           | Syntax / Rule                            |
|-------------------|------------------------------------------|
| Declare           | `int arr[5];`                            |
| Initialize        | `int arr[5] = {1, 2, 3, 4, 5};`         |
| Access element    | `arr[0]` (first), `arr[4]` (last of 5)  |
| Update element    | `arr[2] = 99;`                           |
| Indexing           | Starts at 0                              |
| Size              | Fixed at declaration (static in C)       |
| Element types     | Must all be the same type                |

### Strings
| Concept             | Detail                                   |
|---------------------|------------------------------------------|
| What is a string?   | Array of `char` ending with `\0`         |
| Null terminator     | `\0` (ASCII 0) marks end of string       |
| Index into string   | `name[0]` gets first character           |
| `string` keyword    | CS50 training wheel (really `char *`)    |
| `strlen(s)`         | Returns length (excludes `\0`)           |

### ASCII
| Fact                          | Value          |
|-------------------------------|----------------|
| `'A'` through `'Z'`          | 65–90          |
| `'a'` through `'z'`          | 97–122         |
| Lowercase − Uppercase gap     | 32             |
| `'\0'` (NUL)                 | 0              |
| Letters are sequential        | `'a' < 'b' < 'c'`... |

### Command-Line Arguments
| Concept       | Detail                                             |
|---------------|----------------------------------------------------|
| `argc`        | Number of words typed (count starts at 1)          |
| `argv[]`      | Array of strings (each word)                       |
| `argv[0]`     | Always the program name                            |
| `argv[1]`     | First user argument                                |
| `argv[i][j]`  | Character `j` of argument `i`                      |
| Everything is a string | Even `"13"` is a string, not an int        |
