📋 Arrays (Short by Doug Lloyd @ CS50)

This short digs into arrays more deeply than the main lecture: how they're stored in memory, how to declare and initialize them, multi-dimensional arrays, and a critical distinction — arrays are passed to functions by **reference**, not by value. This changes how functions interact with array data.

---

## 1️⃣ What Is an Array?

An array is a way to store **multiple values of the same data type** in **contiguous (adjacent) memory locations**, all under a single variable name.

Without arrays, storing five scores requires five separate variables:
```c
int score1 = 120;
int score2 = 86;
int score3 = 75;
int score4 = 60;
int score5 = 50;
```
Clunky. Hard to loop. Doesn't scale.

With an array:
```c
int scores[5] = {120, 86, 75, 60, 50};
```
One name, five values, fully loopable.

### The post office box analogy
Picture a wall of PO boxes at a post office:

| PO Box concept | Array concept |
|----------------|---------------|
| The entire wall of boxes | The array (one large block of contiguous memory) |
| One individual box | One element (holds a single value) |
| All boxes are the same size | All elements are the same data type |
| Box number (address) | Index (starts at 0) |
| Contents of a box | The value stored in that element |
| Only certain things fit (letters vs. packages) | Each element must be the declared data type |

You can access any box directly by its number — no need to open box 1 to get to box 5. Same with arrays: `scores[4]` goes straight to the 5th element.

---

## 2️⃣ Declaring an Array

### Syntax
```
type name[size];
```

Three parts — same as a variable declaration, plus `[size]`:
| Part | What it specifies | Example |
|------|------------------|---------|
| `type` | Data type of **every** element | `int`, `double`, `char`, `bool` |
| `name` | The name for the whole array | `student_grades`, `menu_prices` |
| `[size]` | Number of elements (in square brackets) | `[40]`, `[8]`, `[3]` |

### Examples
```c
int student_grades[40];    // 40 integers
double menu_prices[8];     // 8 doubles
bool truthtable[10];       // 10 booleans
```

- **Clarification:** All elements must be the **same type**. You cannot mix `int` and `char` in one array. If you need mixed types, that's a different data structure (structs — a later topic).

---

## 3️⃣ Indexing: Zero-Based

Array indices in C always start at **0**:

```
Array of n elements:

Index:   [0]   [1]   [2]   ...   [n-2]  [n-1]
          ↑                               ↑
        first                           last
```

| Array size | First index | Last index |
|------------|-------------|------------|
| 3 | `[0]` | `[2]` |
| 10 | `[0]` | `[9]` |
| 50 | `[0]` | `[49]` |
| n | `[0]` | `[n-1]` |

### The out-of-bounds danger
```c
int arr[50];
arr[59] = 5;    // compiles fine — no warning!
arr[-3] = 5;    // compiles fine — no warning!
```

C **will not** warn you at compile time if you go out of bounds. At runtime, you may:
- Get a **segmentation fault** (crash)
- Silently corrupt other data in memory (worse — no crash, wrong answers)

**Rule:** Never access an index below `0` or at/above the size of the array.

---

## 4️⃣ Using Array Elements

An individual array element works exactly like any other variable of that type:

```c
bool truthtable[10];

truthtable[2] = false;          // assign a value
truthtable[9] = true;           // assign another

if (truthtable[7] == true)      // use in a condition
{
    printf("TRUE!\n");
}
```

**Out-of-bounds example:**
```c
truthtable[10] = true;   // DANGER — valid indices are 0–9 only
```

This compiles and might even seem to "work" sometimes — but it's writing to memory your program doesn't own. It could overwrite a neighboring variable or cause a segmentation fault. Doug Lloyd calls this behavior "legal C, but not necessarily the best move."

---

## 5️⃣ Initializing Arrays

### Method 1: Individual assignment (one element at a time)
```c
int arr[3];
arr[0] = 10;
arr[1] = 20;
arr[2] = 30;
```

### Method 2: Instantiation syntax (all at once)
```c
int arr[3] = {10, 20, 30};
```

Both methods produce the same array: `{10, 20, 30}`. The second is more concise when you know the values upfront.

### Method 3: Omitting the size (when using instantiation syntax)
```c
bool truthtable[] = {false, true, true};
```

When you provide `{...}`, the compiler counts the elements and sets the size automatically. This array has 3 elements (`truthtable[0]`, `truthtable[1]`, `truthtable[2]`).

### Method 4: Initializing with a loop

For large arrays, use a `for` loop. A great exercise: create an array of 100 integers where each element equals its own index.

```c
int arr[100];
for (int i = 0; i < 100; i++)
{
    arr[i] = i;
}
```

**Result:**
```
arr[0]=0, arr[1]=1, arr[2]=2, ... arr[99]=99
```

| `i` | `arr[i]` set to |
|-----|----------------|
| 0 | 0 |
| 1 | 1 |
| 2 | 2 |
| ... | ... |
| 99 | 99 |

---

## 6️⃣ Multi-Dimensional Arrays

Arrays can have more than one dimension — useful for grids and tables:

```c
bool battleship[10][10];
```

Think of this as a **10×10 grid** — perfect for Battleship, Tic-tac-toe, or any board game:

```
         [0]  [1]  [2]  ...  [9]
[0]       .    .    .         .
[1]       .    .    .         .
...       .    .    .         .
[9]       .    .    .         .
```

Access with double indexing (row, then column):
```c
battleship[2][5] = true;    // row 2, column 5
battleship[0][0] = false;   // top-left corner
```

### Under the hood
In memory, a 2D array is stored as a single continuous block. A `bool battleship[10][10]` is actually 100 booleans laid out sequentially. The 2D grid notation is just a convenient abstraction for human thinking. A 3D array `int cube[5][5][5]` is 125 integers in one block.

- **Why this matters:** The 2D abstraction lets you think "row, column" instead of "element 25 of a flat 100-element array." It doesn't change performance or memory — just readability.

---

## 7️⃣ Arrays Cannot Be Copied with `=`

This is a critical difference between arrays and regular variables.

### What works for single variables
```c
int a = 5;
int b = a;    // b is now 5 — copy works fine
```

### What does NOT work for arrays
```c
int food[5] = {1, 2, 3, 4, 5};
int bar[5];
bar = food;   // ILLEGAL in C — will not compile
```

### The correct way: copy element by element in a loop
```c
int food[5] = {1, 2, 3, 4, 5};
int bar[5];

for (int j = 0; j < 5; j++)
{
    bar[j] = food[j];    // copy one element at a time
}
```

### Step-by-step
| Iteration | `j` | `food[j]` | `bar[j]` after assignment |
|-----------|-----|-----------|---------------------------|
| 1st | 0 | `1` | `1` |
| 2nd | 1 | `2` | `2` |
| 3rd | 2 | `3` | `3` |
| 4th | 3 | `4` | `4` |
| 5th | 4 | `5` | `5` |

After the loop, `bar` = `{1, 2, 3, 4, 5}` — a separate copy of `food`.

- **Clarification:** Modern languages like Python and Java let you assign arrays with `=`. C does not. This is a deliberate design choice — C gives you explicit control over what gets copied and when.

---

## 8️⃣ Arrays Are Passed by Reference

This is the most important and surprising concept in this short.

### Recall: regular variables are passed by value (covered in Scope short)
```c
void set_int(int x)
{
    x = 22;    // modifies the LOCAL copy — no effect on the caller
}

int main(void)
{
    int a = 10;
    set_int(a);
    printf("%i\n", a);   // prints 10 — a is unchanged
}
```

`set_int` gets a copy of `a`. It changes the copy. The original `a` in `main` is untouched.

### Arrays are different — passed by reference
```c
void set_array(int array[4])
{
    array[0] = 22;    // modifies the ACTUAL array — affects the caller!
}

int main(void)
{
    int b[4] = {0, 1, 2, 3};
    set_array(b);
    printf("%i\n", b[0]);   // prints 22 — b[0] WAS changed!
}
```

When you pass an array to a function, the function receives the **actual array** — not a copy. Any changes the function makes to the array elements affect the original array in the caller.

### Why does C do this?
Copying a large array (thousands of elements) every time you call a function would be enormously slow and wasteful. For efficiency, C passes arrays by reference — the function works directly on the original. The trade-off: you must be careful that functions don't accidentally corrupt array data.

- **Note:** The deep reason *why* arrays are passed by reference (involving pointers) will be explained in a later week. For now, just know that it happens.

---

## 9️⃣ Quiz: What Gets Printed?

```c
void set_array(int array[4]);
void set_int(int x);

int main(void)
{
    int a = 10;
    int b[4] = {0, 1, 2, 3};
    set_int(a);
    set_array(b);
    printf("%d %d\n", a, b[0]);
}

void set_array(int array[4])
{
    array[0] = 22;
}

void set_int(int x)
{
    x = 22;
}
```

**Answer: `10 22`**

### Step-by-step trace
| Step | Code | `a` | `b[0]` |
|------|------|-----|--------|
| 1 | `int a = 10` | `10` | — |
| 2 | `int b[4] = {0, 1, 2, 3}` | `10` | `0` |
| 3 | `set_int(a)` called | (copy of `10` passed in) | `0` |
| 4 | Inside `set_int`: `x = 22` — modifies the copy | `a` still `10` ✅ | `0` |
| 5 | `set_array(b)` called | `10` | (actual `b` passed in) |
| 6 | Inside `set_array`: `array[0] = 22` — modifies the actual array | `10` | `22` ✅ |
| 7 | `printf("%d %d\n", a, b[0])` | — | — |

Output: `10 22`

### Why
- `a = 10`: `set_int` received a **copy** of `a` → the original `a` is never touched.
- `b[0] = 22`: `set_array` received the **actual** array `b` → `b[0]` was changed in place.

This directly demonstrates pass by value (integers) vs. pass by reference (arrays).

---

## 🔟 Summary

### Array basics
| Concept | Detail |
|---------|--------|
| Declaration | `type name[size];` |
| Indexing | Starts at 0, ends at `n-1` |
| Out of bounds | C doesn't prevent it — causes segfault or silent data corruption |
| Element access | Works like any variable of that type |
| All same type | You cannot mix types in one array |

### Initialization methods
| Method | Syntax |
|--------|--------|
| Individual | `arr[0] = 10; arr[1] = 20;` |
| Instantiation | `int arr[3] = {10, 20, 30};` |
| Size-inferred | `int arr[] = {10, 20, 30};` (compiler counts elements) |
| Loop | `for (int i = 0; i < n; i++) arr[i] = i;` |

### Key distinctions
| Concept | Regular variable | Array |
|---------|-----------------|-------|
| Copy with `=` | ✅ Works | ❌ Illegal in C — must loop |
| Passed to function | By **value** (copy) | By **reference** (original) |
| Function changes it | No effect on caller | Changes affect the caller |
| Multiple dimensions | N/A | ✅ `int grid[10][10];` |
