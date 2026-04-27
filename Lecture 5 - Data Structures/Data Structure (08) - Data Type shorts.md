🏗️ Structures — Defining Your Own Data Types (Lecture 5 — Data Structures @ CS50)

This short by Doug Lloyd introduces **structures** (`struct`) — C's mechanism for creating brand-new data types by bundling multiple variables of *different* types together into a single "super-variable." This is the foundation of everything in data structures: nodes in linked lists, trees, and hash tables are all built using structs.

---

## 1. 🧠 The Core Idea — What Is a Structure?

So far in C, every variable holds exactly one value of one type: an `int`, a `char`, a `double`. The only way we've combined multiple values is with **arrays** — but arrays have a critical restriction: **every element must be the same type**.

What if you need to represent a *car*? A car has:
- A year → `int`
- A model name → `char[]` (string)
- A license plate → `char[]` (string)
- An odometer reading → `int`
- An engine size → `double`

These are five different types. An array can't hold them together. This is exactly the problem structures solve.

> **A structure is a "super-variable" — a single variable that contains other variables inside it, each of which can be a different type.**

---

## 2. 📋 Structures vs Arrays — Key Difference

| Feature | Array | Structure |
|---------|-------|-----------|
| **What it holds** | Multiple values of the **same** type | Multiple values of **different** types |
| **Access method** | By index: `arr[0]`, `arr[1]` | By field name: `mycar.year`, `mycar.model` |
| **Example** | `int scores[5]` — 5 integers | `struct car` — year + model + plate + odometer + engine |
| **Use case** | A list of similar things | A single complex "thing" with properties |

Think of it this way: an array is like a row of identical lockers. A struct is like a single person — they have a name, an age, a height, and a favourite colour, all bundled together as one entity.

---

## 3. 🔧 Defining a Structure — Syntax

```c
struct car
{
    int year;
    char model[10];
    char plate[7];
    int odometer;
    double engine_size;
};
```

Breaking this down piece by piece:

| Part | Meaning |
|------|---------|
| `struct` | Keyword that says "I'm about to define a new structure type" |
| `car` | The name you're giving this structure type |
| `{ ... }` | The body — everything inside is a **field** (also called a **member**) |
| `int year;` | A field called `year` of type `int` |
| `char model[10];` | A field called `model` — a string of up to 9 characters |
| `char plate[7];` | A field called `plate` — a license plate string (e.g. `"CS50"`) |
| `int odometer;` | A field called `odometer` of type `int` |
| `double engine_size;` | A field called `engine_size` of type `double` |
| `;` at the end | **Required** — this is a common syntax error if forgotten |

> **The semicolon after the closing `}` is mandatory.** Unlike functions, struct definitions must end with `;`. Forgetting it causes confusing compiler errors.

### Memory layout of `struct car`

When you create a `struct car`, the computer allocates memory for all fields back-to-back:

```
struct car in memory:
┌──────────────┬────────────────────┬──────────────┬──────────────┬──────────────────┐
│   year (4B)  │   model (10B)      │  plate (7B)  │odometer (4B) │engine_size (8B)  │
│   int        │   char[10]         │  char[7]     │   int        │   double         │
└──────────────┴────────────────────┴──────────────┴──────────────┴──────────────────┘
 Total: roughly 33+ bytes (may be slightly more due to memory alignment padding)
```

`sizeof(struct car)` tells you the exact size — you never have to calculate it yourself.

---

## 4. 📍 Where to Define Structs

Structs are typically defined in **one of two places**:

### Option A — At the top of your `.c` file (before any functions)

```c
#include <stdio.h>
#include <string.h>

struct car          // ← defined here, outside of main()
{
    int year;
    char model[10];
    char plate[7];
    int odometer;
    double engine_size;
};

int main(void)
{
    // can now use struct car here
}
```

### Option B — In a separate `.h` header file

```c
// car.h
struct car
{
    int year;
    char model[10];
    char plate[7];
    int odometer;
    double engine_size;
};
```

Then in any `.c` file that needs it:

```c
#include "car.h"    // now this file knows what struct car is
```

- **Clarification — why use a .h file?** If multiple `.c` files all need to use `struct car`, putting it in a `.h` file means you define it once and include it everywhere. Without this, you'd have to copy-paste the struct definition into every file — a maintenance nightmare.

---

## 5. 🔵 Creating and Using Struct Variables — The Dot Operator

Once a struct is defined, you create variables of that type just like any other variable:

```c
// Declaring a variable of type struct car
struct car mycar;
```

The **type** is `struct car`. The **variable name** is `mycar`.

To access or modify the fields inside, you use the **dot operator** (`.`):

```c
// Accessing fields with the dot operator
mycar.year = 2011;
strcpy(mycar.plate, "CS50");    // strings must be copied, not assigned with =
mycar.odometer = 50505;
```

- **Clarification — why `strcpy` for strings?** `mycar.plate` is a `char` array, not a pointer. You can't assign strings with `=` in C — you must copy the characters using `strcpy`. Trying `mycar.plate = "CS50"` is a compiler error.

### Visualising `mycar` after these assignments:

```
mycar
┌─────────────┬──────────────┬──────────────┬─────────────┬──────────────────┐
│  year: 2011 │ model: ???   │ plate: "CS50"│ odo: 50505  │ engine_size: ???  │
└─────────────┴──────────────┴──────────────┴─────────────┴──────────────────┘
```

Fields you haven't set yet contain garbage values — same as any uninitialised variable in C.

### The dot operator rule

You always refer to a field **in the context of its variable**:

```c
year = 2011;         // ❌ ERROR — year doesn't exist on its own
mycar.year = 2011;   // ✅ CORRECT — year as a field of mycar
```

`year` alone means nothing to the compiler. It only exists as `mycar.year`.

---

## 6. 🔶 Dynamic Allocation — Structs on the Heap

Like any other type, structs can be allocated on the **heap** (dynamically) instead of the **stack** (statically). You do this when you don't know at compile time how many structs you'll need — for example, when building a linked list where new nodes are added at runtime.

```c
// Dynamic allocation — struct lives on the heap
struct car *mycar = malloc(sizeof(struct car));
```

- `sizeof(struct car)` — asks the compiler for the exact byte size of the struct. You never need to calculate it manually.
- `malloc(...)` — allocates that many bytes on the heap and returns a pointer to the first byte.
- `struct car *mycar` — `mycar` is now a **pointer** to a `struct car`, not the struct itself.

### Memory diagram — stack vs heap

```
Stack                          Heap
─────────────────────          ──────────────────────────────────────
mycar [ 0x200 ] ──────────────► [ year | model | plate | odo | eng ]
  (just a pointer,                       struct car lives here
   8 bytes)
─────────────────────          ──────────────────────────────────────
```

Now `mycar` is a pointer. To access the fields, you must first **dereference** the pointer, then use the dot operator:

```c
(*mycar).year = 2011;
strcpy((*mycar).plate, "CS50");
(*mycar).odometer = 50505;
```

The parentheses around `*mycar` are required because `.` has higher precedence than `*`. Without them, `*mycar.year` would be interpreted as `*(mycar.year)` — which is wrong.

- **Clarification — precedence:** In C, the dot operator (`.`) binds more tightly than the dereference operator (`*`). So `*mycar.year` means "dereference `mycar.year`", but `mycar.year` isn't even a pointer — it's a field. Wrapping `*mycar` in parentheses forces the dereference to happen first, giving you the struct, and then `.year` accesses the field. It's correct but visually messy.

---

## 7. ➡️ The Arrow Operator — `->` (The Shortcut)

Because accessing fields through a pointer (dereference + dot) is so common, C provides a dedicated operator that does both steps in one: the **arrow operator** (`->`).

```
(*mycar).year = 2011;     →    mycar->year = 2011;
```

They are **exactly equivalent**. The arrow operator:
1. **Dereferences** the pointer on its left side
2. **Accesses** the field named on its right side

```c
// Before — with (*ptr).field syntax
struct car *mycar = malloc(sizeof(struct car));
(*mycar).year = 2011;
strcpy((*mycar).plate, "CS50");
(*mycar).odometer = 50505;

// After — with -> syntax (identical behaviour, much cleaner)
struct car *mycar = malloc(sizeof(struct car));
mycar->year = 2011;
strcpy(mycar->plate, "CS50");
mycar->odometer = 50505;
```

### When to use `.` vs `->`

| Situation | Operator | Example |
|-----------|----------|---------|
| Variable declared directly (on the stack) | `.` (dot) | `struct car mycar;` → `mycar.year` |
| Variable declared as a pointer (on the heap) | `->` (arrow) | `struct car *mycar = malloc(...)` → `mycar->year` |

A simple rule: **if you used `*` in the declaration, use `->` to access fields. If you didn't use `*`, use `.`.**

### Visual summary

```
Stack variable:              Pointer variable:
struct car mycar;            struct car *mycar = malloc(...);
      │                              │
      │  .year                       │  ->year
      ▼                              ▼
┌─────────────┐              ┌───────────────┐
│ year: 2011  │              │  year: 2011   │
│ plate: CS50 │              │  plate: CS50  │
│ odo: 50505  │              │  odo: 50505   │
└─────────────┘              └───────────────┘
(struct is the variable)     (pointer → struct on heap)
```

---

## 8. 💻 Complete Example — Both Styles Side by Side

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct car
{
    int year;
    char model[10];
    char plate[7];
    int odometer;
    double engine_size;
};

int main(void)
{
    // ── Style 1: Stack allocation — use dot operator ──
    struct car mycar;
    mycar.year = 2011;
    strcpy(mycar.model, "Civic");
    strcpy(mycar.plate, "CS50");
    mycar.odometer = 50505;
    mycar.engine_size = 1.8;

    printf("Stack car: %d %s\n", mycar.year, mycar.plate);

    // ── Style 2: Heap allocation — use arrow operator ──
    struct car *heapcar = malloc(sizeof(struct car));
    if (heapcar == NULL) return 1;

    heapcar->year = 2024;
    strcpy(heapcar->model, "Tesla");
    strcpy(heapcar->plate, "EV50");
    heapcar->odometer = 1000;
    heapcar->engine_size = 0.0;  // electric!

    printf("Heap car: %d %s\n", heapcar->year, heapcar->plate);

    free(heapcar);   // always free heap memory when done
    return 0;
}
```

---

## 9. 📌 Summary

| Concept | Detail |
|---------|--------|
| **Structure (`struct`)** | A custom data type that bundles multiple fields of different types into one variable |
| **Why use structs?** | Arrays can only hold one type; structs hold many different types together |
| **"Super-variable"** | Doug Lloyd's term — a variable that has other variables inside it |
| **Field / Member** | One variable inside a struct (e.g. `year`, `plate`, `odometer`) |
| **Defining a struct** | `struct name { type field1; type field2; };` — semicolon after `}` is mandatory |
| **Where to define** | Top of `.c` file (before functions), or in a `.h` header file for multi-file use |
| **Stack declaration** | `struct car mycar;` — use `.` (dot) to access fields |
| **Heap declaration** | `struct car *mycar = malloc(sizeof(struct car));` — use `->` (arrow) to access fields |
| **Dot operator (`.`)** | Access a field on a directly-declared struct variable |
| **Arrow operator (`->`)** | Access a field through a pointer — shorthand for `(*ptr).field` |
| **`sizeof(struct car)`** | Tells you the exact byte size of the struct — no manual calculation needed |
| **Strings in structs** | Must use `strcpy()` to assign — cannot use `=` with char arrays |
| **Always `free`** | If you `malloc` a struct, you must `free` it when done |

### The two operators at a glance

```
mycar.year        →  mycar is a struct variable (on the stack)
                     .year accesses the year field directly

mycar->year       →  mycar is a pointer to a struct (on the heap)
                     -> dereferences the pointer AND accesses year
                     equivalent to (*mycar).year
```
