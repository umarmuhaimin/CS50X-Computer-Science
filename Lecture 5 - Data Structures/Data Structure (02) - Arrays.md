📐 Arrays — Revisited (Lecture 5 — Data Structures @ CS50)

Arrays were introduced back in Week 2 as our very first data structure. We revisit them now with fresh eyes — and with our new knowledge of pointers — to understand both their power and their fundamental limitation. This sets up the motivation for everything else this week.

---

## 1. 🧠 What Is an Array?

An **array** is a block of **contiguous memory** — meaning all the values are stored back-to-back, side by side, with no gaps.

```
Memory address:  0x100  0x104  0x108
                 ┌────┐ ┌────┐ ┌────┐
int list[3];     │  1 │ │  2 │ │  3 │
                 └────┘ └────┘ └────┘
```

Because the data is contiguous, the computer can jump directly to any element using simple arithmetic:
- Element at index `i` is at address: `base_address + (i × sizeof(int))`
- This is why `list[0]`, `list[1]`, `list[2]` are instant — no searching needed.

This **contiguous layout** is also why binary search works on arrays: you can always find the exact middle by dividing by 2.

---

## 2. 💻 Basic Array Implementation

```c
// Implements a list of numbers with an array of fixed size
#include <stdio.h>

int main(void)
{
    // List of size 3
    int list[3];

    // Initialize list with numbers
    list[0] = 1;
    list[1] = 2;
    list[2] = 3;

    // Print list
    for (int i = 0; i < 3; i++)
    {
        printf("%i\n", list[i]);
    }
}
```

This is clean, simple, and fast. But what if you want to add a 4th number?

---

## 3. ❌ The Problem: Fixed Size

When you declare `int list[3]`, the operating system gives you **exactly 3 integers' worth of memory** — no more, no less. The bytes immediately after your array may already be used by something else.

```
Memory (conceptual view):
┌──────┬──────┬──────┬──────────────────────────┐
│  1   │  2   │  3   │  "hello\0" (other data)  │
└──────┴──────┴──────┴──────────────────────────┘
  list[0] list[1] list[2]   ← you can't put 4 here!
```

If you try to put a 4th element right after index 2, you'd be **overwriting data that belongs to something else** — corrupting memory.

The solution? You must allocate a **new, bigger block** of memory, copy the old values over, and discard the old block.

---

## 4. 🔧 Solution 1 — malloc + Manual Copy

```c
// Implements a list of numbers with an array of dynamic size
#include <stdio.h>
#include <stdlib.h>

int main(void)
{
    // List of size 3
    int *list = malloc(3 * sizeof(int));
    if (list == NULL)
    {
        return 1;
    }

    // Initialize list of size 3 with numbers
    list[0] = 1;
    list[1] = 2;
    list[2] = 3;

    // List of size 4 (a new, bigger block)
    int *tmp = malloc(4 * sizeof(int));
    if (tmp == NULL)
    {
        free(list);     // ← free old list before aborting!
        return 1;
    }

    // Copy list of size 3 into list of size 4
    for (int i = 0; i < 3; i++)
    {
        tmp[i] = list[i];
    }

    // Add number to list of size 4
    tmp[3] = 4;

    // Free list of size 3
    free(list);

    // Remember list of size 4
    list = tmp;

    // Print list
    for (int i = 0; i < 4; i++)
    {
        printf("%i\n", list[i]);
    }

    // Free list
    free(list);
    return 0;
}
```

### Step-by-step what's happening

```
Step 1: malloc(3 * sizeof(int))
        list → [ 1 | 2 | 3 ]

Step 2: malloc(4 * sizeof(int))
        tmp  → [ ? | ? | ? | ? ]   ← garbage values

Step 3: Copy old into new
        tmp  → [ 1 | 2 | 3 | ? ]

Step 4: Add 4th value
        tmp  → [ 1 | 2 | 3 | 4 ]

Step 5: free(list)       ← give old block back to OS
Step 6: list = tmp       ← now list points to the new block
        list → [ 1 | 2 | 3 | 4 ]
```

### Why use a temporary variable `tmp`? ⚠️

**Never** do this:
```c
list = malloc(4 * sizeof(int));  // BAD! You just lost the old list!
```

If you reassign `list` directly, you lose the address of the original `[ 1 | 2 | 3 ]` block. You can never `free` it, and it becomes a **memory leak** — wasted memory that builds up over time as the program runs.

The `tmp` pointer acts as a safety net: hold the new address here, then only reassign `list` after you've safely freed the old one.

### Two rules when using malloc (always)

1. **Always check for NULL** — `malloc` returns `NULL` if it couldn't allocate memory. If you skip this check and try to use a NULL pointer, your program crashes with a segmentation fault.
2. **Always `free` what you `malloc`** — every chunk of memory you allocate must eventually be released back to the OS. Missing this causes memory leaks.

---

## 5. 🔧 Solution 2 — realloc (Smarter)

Copying the array manually with a for loop is tedious. The standard library provides `realloc`, which does it for you:

```c
// Implements a list of numbers with an array of dynamic size using realloc
#include <stdio.h>
#include <stdlib.h>

int main(void)
{
    // List of size 3
    int *list = malloc(3 * sizeof(int));
    if (list == NULL)
    {
        return 1;
    }

    list[0] = 1;
    list[1] = 2;
    list[2] = 3;

    // Resize list to be of size 4
    int *tmp = realloc(list, 4 * sizeof(int));
    if (tmp == NULL)
    {
        free(list);
        return 1;
    }
    list = tmp;

    // Add number to list
    list[3] = 4;

    // Print list
    for (int i = 0; i < 4; i++)
    {
        printf("%i\n", list[i]);
    }

    // Free list
    free(list);
    return 0;
}
```

### How `realloc` works

`realloc(list, 4 * sizeof(int))` does the following automatically:
1. Checks if there is space right after the existing block to simply extend it — if yes, it does so instantly (very efficient!)
2. If not, it finds a new block of 4 integers elsewhere in memory, copies all the existing data over, and frees the old block for you

Either way, you get back a pointer to memory that holds your old data plus room for the new value.

- **Clarification — why still use `tmp`?** Same reason as before. If `realloc` fails and returns `NULL`, it does **not** touch the original memory. But if you had written `list = realloc(list, ...)` and got `NULL`, you'd have just lost the original `list` pointer — memory leak. Using `tmp` protects you.

---

## 6. ⚖️ Array Trade-offs Summary

| Aspect | Array |
|--------|-------|
| **Memory layout** | Contiguous — all values back-to-back |
| **Access speed** | O(1) — direct index lookup via arithmetic |
| **Binary search** | ✅ Possible — can jump to middle instantly |
| **Resizing** | ❌ Must copy entire array to grow |
| **Fixed vs dynamic** | Statically fixed (or manually resized with malloc/realloc) |
| **Memory waste** | Risk of over-allocating (too big) or under-allocating (too small) |

### The fundamental limitation

Every time you want to add one more element to a full array, you may have to copy the entire array to a new location. For 3 elements, this is trivial. For 3 million elements, this becomes a serious performance problem.

> **This is the motivation for linked lists** — a data structure that avoids copying entirely, letting you add new elements without moving anything that's already in memory.

---

## 7. 📌 Summary

| Concept | Detail |
|---------|--------|
| **Array** | Contiguous block of memory — values stored side by side |
| **Key advantage** | O(1) access; enables binary search O(log n) |
| **Key limitation** | Fixed size; resizing requires copying everything |
| **`malloc(n * sizeof(int))`** | Allocates n integers of memory dynamically |
| **`realloc(ptr, new_size)`** | Resize an existing malloc'd block (copies data for you) |
| **Why use `tmp`** | Protect the original pointer in case realloc fails |
| **Always check NULL** | malloc/realloc return NULL if allocation fails |
| **Always `free`** | Every malloc must eventually be freed |
