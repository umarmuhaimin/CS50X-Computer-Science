💻 search.c — Implementing Linear Search in C (Lecture 3 — Algorithms @ CS50)

So far we've described linear search in pseudocode. Now it's time to write it in actual C. This file covers two implementations: searching an **array of integers**, and then searching an **array of strings**. Along the way, we pick up two important concepts — `return` codes and `strcmp`.

---

## 1. 🔢 Version 1 — Linear Search for Integers

### The full program

```c
// Implements linear search for integers

#include <cs50.h>
#include <stdio.h>

int main(void)
{
    // An array of integers
    int numbers[] = {20, 500, 10, 5, 100, 1, 50};

    // Search for number
    int n = get_int("Number: ");
    for (int i = 0; i < 7; i++)
    {
        if (numbers[i] == n)
        {
            printf("Found\n");
            return 0;
        }
    }
    printf("Not found\n");
    return 1;
}
```

### Line-by-line breakdown

**Array declaration:**
```c
int numbers[] = {20, 500, 10, 5, 100, 1, 50};
```
- Uses **instantiation syntax** — the values are provided upfront, so the size (`7`) is inferred by the compiler.
- Note this array is **unsorted** (`{20, 500, 10, 5, 100, 1, 50}`) — because linear search works on any order, sorting is not required here.

**The array in memory:**
```
Index:   [0]   [1]   [2]  [3]  [4]  [5]  [6]
Value:    20   500    10    5   100    1   50
```

**User input:**
```c
int n = get_int("Number: ");
```
- `get_int` (from `cs50.h`) prompts the user and returns an `int`.

**The for loop:**
```c
for (int i = 0; i < 7; i++)
{
    if (numbers[i] == n)
    {
        printf("Found\n");
        return 0;
    }
}
```
- `i` runs from `0` to `6` — covering all 7 elements.
- `numbers[i] == n` compares each element to the target using `==`. This works for integers.
- If found: print `"Found"` and immediately `return 0` — exits the program right there.

**After the loop:**
```c
printf("Not found\n");
return 1;
```
- Only reached if the loop completed without finding a match.

---

## 2. 🔁 Step-by-Step Walkthrough

### Example: searching for `50`

```
numbers[] = {20, 500, 10, 5, 100, 1, 50}
```

| Step | `i` | `numbers[i]` | `numbers[i] == 50`? | Action |
|------|-----|-------------|---------------------|--------|
| 1 | 0 | 20 | No | Continue |
| 2 | 1 | 500 | No | Continue |
| 3 | 2 | 10 | No | Continue |
| 4 | 3 | 5 | No | Continue |
| 5 | 4 | 100 | No | Continue |
| 6 | 5 | 1 | No | Continue |
| 7 | 6 | 50 | **Yes** | `printf("Found")`, `return 0` |

Output: `Found`

### Example: searching for `42` (not in array)

| Step | `i` | `numbers[i]` | `numbers[i] == 42`? | Action |
|------|-----|-------------|---------------------|--------|
| 1–7 | 0–6 | 20,500,10,5,100,1,50 | No for all | Loop ends |

After loop → `printf("Not found")`, `return 1`

Output: `Not found`

---

## 3. ✅ `return 0` vs `return 1` — Exit Codes

This is an important convention in C and Unix/Linux systems:

| Return value | Meaning | When used |
|-------------|---------|-----------|
| `return 0` | **Success** — the program completed its task | Target was found |
| `return 1` | **Failure / Error** — the program could not complete its task | Target was not found |

- **Clarification:** `return 0` and `return 1` from `main` don't print anything — they signal to the **operating system** whether the program succeeded or failed. This is useful when programs are chained together in scripts. A script can check the exit code to decide what to do next.
- **Clarification — why `return 0` inside the loop?** As soon as we find the target, we're done. There's no reason to keep searching. `return 0` exits the program immediately from wherever it's called — even from inside a loop. This is the same "early exit" guard-clause pattern from Week 2.

---

## 4. 🔤 Version 2 — Linear Search for Strings

Searching strings works the same way structurally, but with one critical difference: **you cannot compare strings with `==`.**

### The full program

```c
// Implements linear search for strings

#include <cs50.h>
#include <stdio.h>
#include <string.h>

int main(void)
{
    // An array of strings
    string strings[] = {"battleship", "boot", "cannon", "iron", "thimble", "top hat"};

    // Search for string
    string s = get_string("String: ");
    for (int i = 0; i < 6; i++)
    {
        if (strcmp(strings[i], s) == 0)
        {
            printf("Found\n");
            return 0;
        }
    }
    printf("Not found\n");
    return 1;
}
```

### What changed from the integer version?

| Part | Integer version | String version | Why |
|------|----------------|----------------|-----|
| Array type | `int numbers[]` | `string strings[]` | Different data type |
| Input function | `get_int` | `get_string` | Match the type |
| Comparison | `numbers[i] == n` | `strcmp(strings[i], s) == 0` | `==` doesn't work on strings |
| Library added | — | `#include <string.h>` | `strcmp` lives here |
| Array size | 7 | 6 | Different number of elements |

---

## 5. 🔡 Why `==` Doesn't Work for Strings — `strcmp` Explained

### The problem with `==` on strings

In C, a `string` is not a simple value like an `int`. It is stored as an **array of characters** somewhere in memory, and the variable holds the **memory address** of where those characters are stored.

When you write `strings[i] == s`, C compares:
- The **address** where `strings[i]`'s characters live
- The **address** where `s`'s characters live

These are two different locations in memory — even if both spell out `"boot"`. The addresses will never be equal, so `==` will always return `false` for strings. You'd never find anything.

### The solution: `strcmp`

`strcmp` (string compare) from `<string.h>` compares strings **character by character**:

```c
strcmp(strings[i], s) == 0
```

| `strcmp` return value | Meaning |
|----------------------|---------|
| `0` | Strings are **identical** — every character matches |
| Negative | First string comes **before** second alphabetically |
| Positive | First string comes **after** second alphabetically |

For a search, we only care about the `== 0` case (exact match).

### Step-by-step: how `strcmp` works on `"boot"` vs `"boot"`

```
strings[i] = "boot"  →  'b', 'o', 'o', 't', '\0'
s          = "boot"  →  'b', 'o', 'o', 't', '\0'

Compare 'b' == 'b' ✅
Compare 'o' == 'o' ✅
Compare 'o' == 'o' ✅
Compare 't' == 't' ✅
Compare '\0' == '\0' ✅ — end of both strings

→ All characters match → return 0
```

### Step-by-step: how `strcmp` works on `"boot"` vs `"cannon"`

```
'b' vs 'c'  →  'b' (98) < 'c' (99)  →  return negative number
→ Stop immediately — "boot" comes before "cannon" alphabetically
```

`strcmp` is efficient — it stops as soon as it finds a difference.

---

## 6. ⚠️ The Hard-Coded Size Problem & Segmentation Faults

```c
for (int i = 0; i < 6; i++)   // 6 is hard-coded
```

The raw notes specifically call this out: **hard-coding the array length is not good programming practice.**

### Why is it bad?

| Problem | Consequence |
|---------|-------------|
| If you add a 7th string to the array but forget to change `6` to `7` | The last element is never checked — silent bug |
| If you change `6` to `7` but only have 6 strings | `strings[6]` is **out of bounds** — segmentation fault |

### What is a segmentation fault?

A **segmentation fault** (segfault) happens when your program tries to access memory it isn't allowed to access. It's C's way of crashing when you read or write beyond your array's allocated space.

```
strings[] has 6 elements → indices 0 to 5
If i < 7 is used → strings[6] is out of bounds → SEGFAULT 💥
```

### The better approach (preview of what's coming)

Instead of hard-coding `7` or `6`, use a pattern like:
```c
int size = sizeof(numbers) / sizeof(numbers[0]);
```
- `sizeof(numbers)` = total bytes the array occupies
- `sizeof(numbers[0])` = bytes for one element
- Dividing gives you the element count — automatically correct even if the array changes.

---

## 7. 📌 Summary

### Two implementations of linear search

| Aspect | Integer search | String search |
|--------|--------------|--------------|
| Array | `int numbers[]` | `string strings[]` |
| Comparison | `numbers[i] == n` | `strcmp(strings[i], s) == 0` |
| Library needed | `cs50.h`, `stdio.h` | `cs50.h`, `stdio.h`, `string.h` |

### Key rules

| Rule | Detail |
|------|--------|
| `==` on integers | ✅ Works — compares values directly |
| `==` on strings | ❌ Never — compares memory addresses, not characters |
| `strcmp(a, b) == 0` | ✅ Correct way to check if two strings are identical |
| `return 0` | Success — task completed (target found) |
| `return 1` | Failure — task not completed (target not found) |
| Hard-coded array size | ⚠️ Bad practice — use calculated size or a constant |
| Out-of-bounds access | 💥 Segmentation fault — program crash |

### Running time

| Algorithm | Big O | Omega |
|-----------|-------|-------|
| Linear search (integers) | O(n) | Ω(1) |
| Linear search (strings) | O(n) | Ω(1) |

Same performance — the data type doesn't change the algorithm's growth class.
