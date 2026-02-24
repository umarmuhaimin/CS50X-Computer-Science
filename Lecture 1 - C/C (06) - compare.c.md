📊 C (06) - compare.c

1) Goal
 - Read two integers, compare them, and print the relationship.
 - Use when you need to report how two numbers relate (less, greater, equal).

2) First pass (only “less than”)

```c
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    int x = get_int("What's x? ");
    int y = get_int("What's y? ");

    if (x < y)
    {
        printf("x is less than y\n");
    }
}
```

 - `get_int` asks the user for numbers and stores them in `x` and `y`.
 - Only handles the case where x < y; other cases print nothing.
 - Outputs:
   - x=2, y=5 → `x is less than y`
   - x=5, y=2 → (no output in this first version)
   - x=3, y=3 → (no output in this first version)

3) Complete version (all cases)

```c
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    int x = get_int("What's x? ");
    int y = get_int("What's y? ");

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
}
```

 - Now every outcome is covered: less than, greater than, or equal.
 - When to use: anytime you must compare two numbers and handle each possible result.
 - Outputs:
   - x=2, y=5 → `x is less than y`
   - x=5, y=2 → `x is greater than y`
   - x=3, y=3 → `x is equal to y`

4) Run it
 - Compile: `make compare`
 - Run: `./compare`
 - If errors appear, re-check spelling, braces, and semicolons.
 - Test with different x, y to see each branch fire.
