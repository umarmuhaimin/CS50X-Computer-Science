🏗️ Structs — Creating Custom Data Types in C (Lecture 3 — Algorithms @ CS50)

In the previous file, we hit a wall: two parallel arrays representing related data with no enforced connection between them. C's built-in types (`int`, `string`, `bool`, etc.) can only hold one kind of value. What we really need is a **single type that holds multiple pieces of related data together** — like a person's name and phone number bundled as one unit. C gives us exactly that tool: **structs**.

---

## 1. 🧠 The Concept — Custom Data Types

C's built-in types:
```
int      → holds a whole number
float    → holds a decimal number
char     → holds a single character
string   → holds a sequence of characters
bool     → holds true or false
```

But what about a **person** — something that has a name AND a number? Or a **student** — who has a name, an ID, and a GPA? C doesn't have these built in. With `typedef struct`, you can build them yourself.

The idea: define a new type that **groups related variables together** as a single named unit. Once defined, you use it exactly like any built-in type.

---

## 2. 💡 Defining a Struct — `typedef struct`

### Syntax

```c
typedef struct
{
    type field_name_1;
    type field_name_2;
    // ... more fields
} type_name;
```

| Part | Meaning |
|------|---------|
| `typedef` | "I'm defining a new type name" |
| `struct` | "It's a structure — a bundle of variables" |
| `{ ... }` | The fields that every instance of this type will contain |
| `type_name` | The name you give to this new custom type |

### The `person` struct

```c
typedef struct
{
    string name;
    string number;
} person;
```

This creates a new data type called `person`. Every `person` contains:
- A `string` called `name`
- A `string` called `number`

- **Clarification — `typedef`:** Without `typedef`, you'd have to write `struct person` every time you use it. `typedef` lets you simply write `person` — it creates an **alias** for the struct type, making the code cleaner.
- **Clarification — fields vs. variables:** The items inside a struct (`name`, `number`) are called **fields** or **members**. They are not standalone variables — they belong to and live inside an instance of the struct.

---

## 3. 💻 The Improved Phonebook — Using Structs

### Full program

```c
// Implements a phone book with structs

#include <cs50.h>
#include <stdio.h>
#include <string.h>

typedef struct
{
    string name;
    string number;
} person;

int main(void)
{
    person people[3];

    people[0].name   = "Kelly";
    people[0].number = "+1-617-495-1000";

    people[1].name   = "David";
    people[1].number = "+1-617-495-1000";

    people[2].name   = "John";
    people[2].number = "+1-949-468-2750";

    // Search for name
    string name = get_string("Name: ");
    for (int i = 0; i < 3; i++)
    {
        if (strcmp(people[i].name, name) == 0)
        {
            printf("Found %s\n", people[i].number);
            return 0;
        }
    }
    printf("Not found\n");
    return 1;
}
```

---

## 4. 🔎 Line-by-Line Breakdown

### Struct definition (above `main`)

```c
typedef struct
{
    string name;
    string number;
} person;
```

This goes **above `main`**, so the compiler knows the `person` type exists before it's used anywhere. Think of it like a function prototype — a heads-up to the compiler.

### Declaring an array of structs

```c
person people[3];
```

- `person` — the type (our custom type, just defined above)
- `people` — the name of the array
- `[3]` — 3 slots, each of type `person`

This is exactly like declaring `int numbers[3]` — except instead of each slot holding one `int`, each slot holds one whole `person` (with both a `name` and a `number` inside it).

**What `people` looks like in memory:**
```
people[0]:  { name: ???,     number: ???             }
people[1]:  { name: ???,     number: ???             }
people[2]:  { name: ???,     number: ???             }
```
(Fields are uninitialized until we assign them.)

### Dot notation — accessing fields

```c
people[0].name   = "Kelly";
people[0].number = "+1-617-495-1000";
```

The **dot (`.`)** is the **member access operator**. It lets you reach inside a struct and access a specific field.

| Expression | Meaning |
|-----------|---------|
| `people[0]` | The entire first `person` struct |
| `people[0].name` | The `name` field of the first person |
| `people[0].number` | The `number` field of the first person |
| `people[2].name` | The `name` field of the third person |

**After all assignments:**
```
people[0]:  { name: "Kelly",  number: "+1-617-495-1000" }
people[1]:  { name: "David",  number: "+1-617-495-1000" }
people[2]:  { name: "John",   number: "+1-949-468-2750" }
```

Now name and number are **locked together inside the same struct**. There is no way to update one without the other being right there with it.

### The search loop

```c
for (int i = 0; i < 3; i++)
{
    if (strcmp(people[i].name, name) == 0)
    {
        printf("Found %s\n", people[i].number);
        return 0;
    }
}
```

- `people[i].name` — access the `name` field of the i-th person for comparison
- `people[i].number` — access the `number` field of the **same** person (same struct, same `i`)

The critical difference from the parallel array version:
- Before: `names[i]` and `numbers[i]` were **two separate arrays** with an implied link
- Now: `people[i].name` and `people[i].number` are **two fields of the same object** — they are physically stored together and cannot be separated

---

## 5. 🔁 Step-by-Step Walkthrough

### Example: searching for `"John"`

```
people[0] = { "Kelly", "+1-617-495-1000" }
people[1] = { "David", "+1-617-495-1000" }
people[2] = { "John",  "+1-949-468-2750" }
```

| Step | `i` | `people[i].name` | Match with `"John"`? | Action |
|------|-----|-----------------|----------------------|--------|
| 1 | 0 | `"Kelly"` | No | Continue |
| 2 | 1 | `"David"` | No | Continue |
| 3 | 2 | `"John"` | **Yes** | `printf("Found +1-949-468-2750")`, `return 0` |

Output: `Found +1-949-468-2750`

### Example: searching for `"Carter"` (not in list)

| Step | `i` | `people[i].name` | Match? | Action |
|------|-----|-----------------|--------|--------|
| 1 | 0 | `"Kelly"` | No | Continue |
| 2 | 1 | `"David"` | No | Continue |
| 3 | 2 | `"John"` | No | Loop ends |

After loop → `printf("Not found")`, `return 1`

Output: `Not found`

---

## 6. ⚖️ Parallel Arrays vs. Structs — Side by Side

### Data storage comparison

**Before (parallel arrays):**
```c
string names[]   = {"Kelly", "David", "John"};
string numbers[] = {"+1-617-495-1000", "+1-617-495-1000", "+1-949-468-2750"};
```
```
names:    [Kelly]  [David]  [John]    ← completely separate array
numbers:  [+1-617] [+1-617] [+1-949] ← the connection is only in the programmer's head
```

**After (structs):**
```c
person people[3];
```
```
people: [ {Kelly, +1-617} | {David, +1-617} | {John, +1-949} ]
          ↑ one unit         ↑ one unit         ↑ one unit
```

### Feature comparison

| Aspect | Parallel arrays | Structs |
|--------|----------------|---------|
| Related data kept together | ❌ Implied by index only | ✅ Physically stored together |
| Can desynchronise | ✅ Yes — easy to forget to update both | ❌ No — fields live in the same struct |
| Adding a new field | Add a whole new separate array | Add one line inside the struct definition |
| Readability | `names[i]` and `numbers[i]` — easy to lose track | `people[i].name` and `people[i].number` — self-documenting |
| Correctness guarantee | None — relies on programmer discipline | Enforced by the type system |
| Scales to 3 fields | 3 separate arrays | 3 fields inside one struct |
| Scales to 100 fields | 100 separate arrays — chaos | 100 fields in one well-defined struct |

---

## 7. 🌱 Extending the Struct (Looking Ahead)

The `person` struct currently holds two `string` fields. But a struct can hold **any number of fields of any types**:

```c
typedef struct
{
    string name;
    string number;
    int    age;
    float  gpa;
    bool   enrolled;
} student;
```

Each `student` would carry all five pieces of information in one bundle. This is the foundation for:
- **Custom data types** used throughout CS50 (e.g., the `node` struct in Week 5 for linked lists)
- **Object-like structures** — structs are C's version of what other languages call objects or records
- **Database records** — a row in a database table is essentially a struct

---

## 8. 📌 Summary

### Struct definition syntax

```c
typedef struct
{
    type field1;
    type field2;
    // ...
} TypeName;
```

### Dot notation

```c
people[i].name    // access the name field of the i-th person
people[i].number  // access the number field of the i-th person
```

### Key concepts

| Concept | Detail |
|---------|--------|
| `struct` | A bundle of variables of different types grouped into one unit |
| `typedef` | Creates a clean alias so you write `person` instead of `struct person` |
| Field / member | A named variable inside a struct — accessed via dot notation |
| Dot operator `.` | Accesses a specific field of a struct instance |
| Array of structs | `person people[3]` — each element is a full `person` struct |
| Why structs beat parallel arrays | Fields are physically co-located — impossible to desynchronise |

### The design progression in these three files

| File | Approach | Problem |
|------|----------|---------|
| Algorithm (03) — search.c | Search integers, then strings | Works but only one type at a time |
| Algorithm (04) — phonebook.c | Two parallel arrays | Data can desynchronise — fragile |
| Algorithm (05) — Structs | Custom `person` type with fields | ✅ Data is bundled — safe and scalable |

Each step solved a problem from the previous one. This is how real programs evolve.
