📒 phonebook.c — Combining Searches & The Problem with Parallel Arrays (Lecture 3 — Algorithms @ CS50)

We've searched integers. We've searched strings. Now we combine both ideas into a real, practical program — a phone book. Along the way, we discover a fundamental design flaw in using two separate arrays to represent related data, and set up the motivation for **structs** (the solution in the next file).

---

## 1. 🧩 The Goal — Searching by Name, Returning a Number

We want a program that:
1. Stores a list of **names** and **phone numbers**
2. Asks the user for a **name**
3. Searches through the names and **returns the matching phone number**

This requires connecting two pieces of data: a name and its corresponding number. The first attempt uses **two separate arrays** running in parallel.

---

## 2. 💻 The Full Program

```c
// Implements a phone book without structs

#include <cs50.h>
#include <stdio.h>
#include <string.h>

int main(void)
{
    // Arrays of strings
    string names[]   = {"Kelly", "David", "John"};
    string numbers[] = {"+1-617-495-1000", "+1-617-495-1000", "+1-949-468-2750"};

    // Search for name
    string name = get_string("Name: ");
    for (int i = 0; i < 3; i++)
    {
        if (strcmp(names[i], name) == 0)
        {
            printf("Found %s\n", numbers[i]);
            return 0;
        }
    }
    printf("Not found\n");
    return 1;
}
```

---

## 3. 🔎 Line-by-Line Breakdown

### The two parallel arrays

```c
string names[]   = {"Kelly", "David", "John"};
string numbers[] = {"+1-617-495-1000", "+1-617-495-1000", "+1-949-468-2750"};
```

These two arrays are designed to line up **index by index**:

| Index | `names[i]` | `numbers[i]` |
|-------|-----------|-------------|
| `[0]` | `"Kelly"` | `"+1-617-495-1000"` |
| `[1]` | `"David"` | `"+1-617-495-1000"` |
| `[2]` | `"John"` | `"+1-949-468-2750"` |

The connection between a name and its number is **implied by sharing the same index**. There is no explicit link between them in the code — the programmer must maintain this correspondence manually.

### The search loop

```c
for (int i = 0; i < 3; i++)
{
    if (strcmp(names[i], name) == 0)
    {
        printf("Found %s\n", numbers[i]);
        return 0;
    }
}
```

- We search through `names[]` using `strcmp` (as learned in search.c).
- When a match is found at index `i`, we use **the same index `i`** to retrieve `numbers[i]`.
- This is the core trick of the parallel array approach: the relationship is maintained through the shared index.

---

## 4. 🔁 Step-by-Step Walkthrough

### Example: searching for `"John"`

```
names[]   = {"Kelly", "David", "John"}
numbers[] = {"+1-617-495-1000", "+1-617-495-1000", "+1-949-468-2750"}
```

| Step | `i` | `names[i]` | `strcmp(names[i], "John") == 0`? | Action |
|------|-----|-----------|----------------------------------|--------|
| 1 | 0 | `"Kelly"` | No — 'K' ≠ 'J' | Continue |
| 2 | 1 | `"David"` | No — 'D' ≠ 'J' | Continue |
| 3 | 2 | `"John"` | **Yes** | `printf("Found +1-949-468-2750")`, `return 0` |

Output: `Found +1-949-468-2750`

### Example: searching for `"Carter"` (not in the list)

| Step | `i` | `names[i]` | Match? | Action |
|------|-----|-----------|--------|--------|
| 1 | 0 | `"Kelly"` | No | Continue |
| 2 | 1 | `"David"` | No | Continue |
| 3 | 2 | `"John"` | No | Loop ends |

After loop → `printf("Not found")`, `return 1`

Output: `Not found`

---

## 5. ⚠️ The Design Problem — Parallel Arrays Are Dangerous

The program works — but it has a serious **design flaw**. The raw notes explicitly flag this: *"there is a chance that names and phone numbers may not correspond to one another."*

### What can go wrong?

**Scenario 1 — Adding a new entry inconsistently:**
```c
// Correct — both arrays updated together
string names[]   = {"Kelly", "David", "John", "Emma"};
string numbers[] = {"+1-617-495-1000", "+1-617-495-1000", "+1-949-468-2750", "+1-800-123-4567"};
```

```c
// BUG — names updated, numbers forgotten
string names[]   = {"Kelly", "David", "John", "Emma"};
string numbers[] = {"+1-617-495-1000", "+1-617-495-1000", "+1-949-468-2750"};
// Now names[3] = "Emma" but numbers[3] is OUT OF BOUNDS → segfault or garbage value
```

**Scenario 2 — Reordering names without reordering numbers:**
```c
// Original
string names[]   = {"Kelly", "David", "John"};
string numbers[] = {"+1-617-495-1000", "+1-617-495-1000", "+1-949-468-2750"};

// Someone alphabetically re-sorts names[], but forgets to re-sort numbers[]
string names[]   = {"David", "John", "Kelly"};
string numbers[] = {"+1-617-495-1000", "+1-617-495-1000", "+1-949-468-2750"};

// Now: David → +1-617 ✅ (coincidence), John → +1-617 ❌ (wrong!), Kelly → +1-949 ❌ (wrong!)
```

The arrays have become **desynchronised** — the program runs without crashing, but silently returns wrong phone numbers.

### The root problem

| Issue | Explanation |
|-------|------------|
| No enforced link | Nothing in C forces `names[i]` and `numbers[i]` to stay paired |
| Programmer discipline | The only thing keeping them in sync is the programmer remembering to update both |
| Scales badly | With 3 entries it's manageable. With 3,000 entries across a team of developers, mistakes are inevitable |
| No single "person" object | A name and a number are logically one thing — a person — but the code treats them as two independent things |

### The solution teased

The raw notes ask: *"Wouldn't it be nice if we could create our own data type where we could associate a person with their phone number?"*

This is exactly what **structs** solve — covered in the next file (Algorithm 05). Instead of two separate arrays, we create a single `person` type that bundles the name and number together, making it impossible to have one without the other.

---

## 6. 📌 Summary

### What this program does
- Searches a `names[]` array using `strcmp`
- When a match is found at index `i`, retrieves `numbers[i]` — the number at the same position
- Demonstrates that search can be used to look up *associated* data, not just confirm presence

### The parallel array pattern

| Strength | Weakness |
|----------|---------|
| Simple to understand | No enforced link between arrays |
| Works fine for small, stable data | Breaks silently if arrays get out of sync |
| Quick to implement | Doesn't scale to larger, more complex programs |

### Key takeaways

| Concept | Detail |
|---------|--------|
| `strcmp` for names | String comparison — same as in search.c |
| `numbers[i]` lookup | Uses the **same index** from the name match to retrieve the number |
| `printf("Found %s\n", numbers[i])` | `%s` formats a string — prints the phone number |
| `return 0` / `return 1` | Success / failure exit codes — same convention as before |
| Parallel array risk | Both arrays must be updated together manually — easy to forget |
| Why structs? | To bundle related data into one unit — the purpose of Algorithm (05) |
