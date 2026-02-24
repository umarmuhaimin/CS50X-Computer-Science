🔁 C (08) - Loops and meow.c

1) Why loops
 - Repeating lines manually is messy. Loops repeat work for us.
 - Use loops to avoid copy-paste, reduce bugs, and make changes in one place.
 - Choose the loop style that matches how you control repetition (known count vs condition).

2) Starting point (repetition)

```c
#include <stdio.h>

int main(void)
{
    printf("meow\n");
    printf("meow\n");
    printf("meow\n");
}
```

 - Works, but code is duplicated.
 - Output:
```
meow
meow
meow
```

3) While loop (counting down)

```c
int i = 3;
while (i > 0)
{
    printf("meow\n");
    i--;
}
```

 - Use when you want to loop while a condition stays true.
 - Good when you don’t know how many times in advance but can express a stop condition.
 - Output:
```
meow
meow
meow
```

4) While loop (counting up, start at 0)

```c
int i = 0;
while (i < 3)
{
    printf("meow\n");
    i++;
}
```

 - Counting from 0 is common in CS.
 - Same as above, but shows inclusive/exclusive bounds: runs while `i < 3`.
 - Output (same):
```
meow
meow
meow
```

5) For loop (compact)

```c
for (int i = 0; i < 3; i++)
{
    printf("meow\n");
}
```

 - Use for loops when you know the number of repeats.
 - Everything about the loop (start, test, step) is in one line—easy to read.
 - Preferred for fixed counts; less risk of forgetting to increment.
 - Output:
```
meow
meow
meow
```

6) Infinite loop (careful!)

```c
while (true)
{
    printf("meow\n");
}
```

 - Runs forever; stop with Ctrl+C.
 - Use intentionally for servers, event loops, or when you’ll break manually inside.
 - Add a `break` condition when you want to exit safely.
 - Output: repeats `meow` endlessly until interrupted.

7) Ask user how many times

```c
int n = get_int("What's n? ");
for (int i = 0; i < n; i++)
{
    printf("meow\n");
}
```

 - `n` comes from the user.
 - Combine user input with loops to make programs dynamic.
 - Output: `meow` printed `n` times (depends on input).

8) Reject negative input (better validation)

Poor design (only checks once):

```c
int n = get_int("What's n? ");
if (n < 0)
{
    n = get_int("What's n? ");
}
```

 - Fails if the user enters negative twice.
 - Shows why single checks are fragile—use a loop to enforce rules.

Loop with continue/break:

```c
int n;
while (true)
{
    n = get_int("What's n? ");
    if (n < 0)
    {
        continue; // ask again
    }
    else
    {
        break;    // good input
    }
}
```

Simpler break-only version:

```c
int n;
while (true)
{
    n = get_int("What's n? ");
    if (n >= 0)
    {
        break;
    }
}
```

Do-while version (runs ask at least once):

```c
int n;
do
{
    n = get_int("What's n? ");
}
while (n < 0);
```

 - Use input-validation loops whenever user input must meet a rule (like non-negative).
 - `continue` skips to the next iteration; `break` exits the loop.
 - Pick the pattern that is simplest to read for the team.
 - Output for these validation blocks: none until valid `n` is entered; then program continues.

9) Final loop to print

```c
for (int i = 0; i < n; i++)
{
    printf("meow\n");
}
```

10) Takeaway
 - Prefer loops over copy-pasted lines.
 - Pick `for` when the count is known; `while`/`do-while` when you loop until a condition changes (like valid input).
 - Guard user input with validation loops; use `break`/`continue` judiciously.
 - Start counts at 0 by habit; be explicit about bounds (`<`, `<=`) to avoid off-by-one errors.
