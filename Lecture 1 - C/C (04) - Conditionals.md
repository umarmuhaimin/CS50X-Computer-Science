🔀 C (04) - Conditionals

1) Why conditionals
 - Scratch used conditionals; in C we also branch on conditions (e.g., do one thing if x > y, else another).
 - Use them whenever you need the program to choose between paths based on a test (numbers, strings, booleans, etc.).
 - They keep logic clear: only one path runs per decision block.

2) Two-way branch

```c
// Conditionals that are mutually exclusive

if (x < y)
{
    printf("x is less than y\n");
}
else
{
    printf("x is not less than y\n");
}
```

 - If x < y → first branch; otherwise → second branch.
 - Use a simple if/else when you only have two options.
 - Output examples:
   - x=2, y=5 → `x is less than y`
   - x=5, y=2 → `x is not less than y`

3) Three-way branch (initial)

```c
// Conditional that isn't necessary

if (x < y)
{
    printf("x is less than y\n");
}
else if (x > y)
{
    printf("x is greater than y\n");
}
else if (x == y)
{
    printf("x is equal to y\n");
}
```

 - Note: the last `else if (x == y)` is redundant.
 - Output examples:
   - x=1, y=3 → `x is less than y`
   - x=4, y=2 → `x is greater than y`
   - x=2, y=2 → `x is equal to y`
 - This style works, but it repeats a comparison you don’t need.

4) Cleaner three-way branch

```c
// Compare integers

if (x < y)
{
    printf("x is less than y\n");
}
else if (x > y)
{
    printf("x is greater than y\n");
}
else
{
    printf("x is equal to y\n");
}
```

 - Final `else` covers equality; no extra comparison needed.
 - Use if/else if/else for multiple cases; let `else` capture the “everything else” bucket.
 - Outputs (same as above):
   - x=1, y=3 → `x is less than y`
   - x=4, y=2 → `x is greater than y`
   - x=2, y=2 → `x is equal to y`
 - Use if/else if/else when you have multiple possibilities; let the final `else` catch “everything else,” like equality here.
