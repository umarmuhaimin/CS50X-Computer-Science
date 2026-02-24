✅ C (07) - agree.c

1) Goal
 - Ask the user to agree (y/n) and respond accordingly.
 - Uses `char` (single character) instead of `string` (many characters).
 - Use when you need a simple yes/no input.

2) Basic version (lowercase only)

```c
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    char c = get_char("Do you agree? ");

    if (c == 'y')
    {
        printf("Agreed.\n");
    }
    else if (c == 'n')
    {
        printf("Not agreed.\n");
    }
}
```

 - Single quotes for single characters; double quotes for strings.
 - `==` checks equality; a single `=` would assign instead.
 - Outputs:
   - c='y' → `Agreed.`
   - c='n' → `Not agreed.`
   - any other char → no output (in this basic version)

3) Handling upper- and lowercase (less efficient)

```c
if (c == 'y')
{
    printf("Agreed.\n");
}
else if (c == 'Y')
{
    printf("Agreed.\n");
}
else
{
    printf("Not agreed.\n");
}
```

 - Works, but repeats code.
 - Outputs:
   - 'y' or 'Y' → `Agreed.`
   - anything else → `Not agreed.`

4) Cleaner using logical OR

```c
if (c == 'Y' || c == 'y')
{
    printf("Agreed.\n");
}
else
{
    printf("Not agreed.\n");
}
```

 - `||` means “or” — one test covers both cases.
 - When to use: anytime you need to check for multiple acceptable inputs in one go.
 - Outputs:
   - 'y' or 'Y' → `Agreed.`
   - any other char → `Not agreed.`

5) Run it
 - Compile: `make agree`
 - Run: `./agree`
 - Test with y, Y, n, or other characters and see the responses.
 - If you need to accept more answers (e.g., 'yes'), extend the condition or map inputs first.
