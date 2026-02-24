🧩 C (09) - Functions

1) Why functions
 - Break code into reusable chunks; name a task and call it when needed.
 - Avoid duplication, make code easier to read, and isolate changes.
 - Use functions when a task is repeated or logically self-contained.

2) Small function (no inputs, no return)

```c
void meow(void)
{
    printf("meow\n");
}
```

 - First `void`: returns nothing. `(void)`: takes no inputs.
 - Output when called once:
```
meow
```
 - When to use: a helper that just does something and doesn’t need data.

Use it in `main` with a prototype:

```c
#include <stdio.h>

void meow(void); // prototype

int main(void)
{
    for (int i = 0; i < 3; i++)
    {
        meow();
    }
}

void meow(void)
{
    printf("meow\n");
}
```

 - Prototype lets `main` know the function exists below.
 - Output:
```
meow
meow
meow
```
 - Why: prototype is needed in C so the compiler knows the function’s signature before use.

3) Add a parameter (tell it how many times)

```c
void meow(int n);

int main(void)
{
    meow(3);
}

void meow(int n)
{
    for (int i = 0; i < n; i++)
    {
        printf("meow\n");
    }
}
```

 - `(int n)` means it expects an int argument.
 - Output (here n=3):
```
meow
meow
meow
```
 - When: same action, but you need to vary it with input (e.g., count).

4) Scope (who can see a variable)

```c
int main(void)
{
    int n = 3; // only in main
    meow(n);   // pass a copy to meow
}

void meow(int n)
{
    for (int i = 0; i < n; i++)
    {
        printf("meow\n");
    }
}
```

 - `n` in `main` is separate from `n` inside `meow`; meow gets its own copy.
 - Output (n=3): same three “meow” lines.
 - Why: each function has its own local variables; passing copies avoids unintended side effects.

5) Getting user input, then calling

```c
#include <cs50.h>
#include <stdio.h>

void meow(int n);

int main(void)
{
    int n;
    do
    {
        n = get_int("Number: ");
    }
    while (n < 1);
    meow(n);
}
```

 - Use a do-while to force at least one prompt and ensure n >= 1.
 - Output: prompts until n>=1, then prints `meow` n times.
 - When: you must validate input before using it in a function.

6) Returning values (helper function)

```c
#include <cs50.h>
#include <stdio.h>

int get_positive_int(void);
void meow(int n);

int main(void)
{
    int n = get_positive_int();
    meow(n);
}

int get_positive_int(void)
{
    int n;
    do
    {
        n = get_int("Number: ");
    }
    while (n < 1);
    return n; // give n back to main
}
```

 - `get_positive_int` returns an int; caller decides what to do with it.
 - Output: prompts until positive; then prints `meow` that many times.
 - Why: returning values makes helpers reusable; they don’t print, they hand back data.

7) When to create a function
 - You repeat a task in several places.
 - A task is conceptually one unit (e.g., “meow n times”).
 - You want to hide details and make main easier to read.
 - You need to test or reuse logic independently.
 - You want clearer intent via names instead of inline code.
