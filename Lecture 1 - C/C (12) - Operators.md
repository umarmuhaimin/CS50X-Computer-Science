➕ C (12) - Operators

1) What operators are in play 🧠
- `+` add, `-` subtract, `*` multiply, `/` divide, `%` remainder (modulo).
- We use these across the course for arithmetic and logic.

2) Add ints (with temp) ➕
```c
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    int x = get_int("What's x? ");
    int y = get_int("What's y? ");
    int z = x + y;
    printf("%i\n", z);
}
```
- Why: clear steps—store sum, then print.
- Output example: x=3, y=5 → `8`

3) Add ints (inline, no temp) 🔹
```c
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    int x = get_int("What's x? ");
    int y = get_int("What's y? ");
    printf("%i\n", x + y);
}
```
- Why: more concise; fewer variables.
- Output: x=3, y=5 → `8`

4) Multiply ints (double a number) ✖️
```c
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    int x = get_int("What's x? ");
    printf("%i\n", x * 2);
}
```
- Shows `*` in action. x=7 → `14`

5) Integer overflow ⚠️
```c
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    int dollars = 1;
    while (true)
    {
        char c = get_char("Here's $%i. Double it and give to next person? ", dollars);
        if (c == 'y')
        {
            dollars *= 2;
        }
        else
        {
            break;
        }
    }
    printf("Here's $%i.\n", dollars);
}
```
- Why: ints have max value (~2,147,483,647 for 32-bit). Doubling past max wraps around (overflow).
- When to use `int`: small ranges are fine; watch for growth.
- Output: keeps doubling until overflow (eventually negative or zero).

6) Use a bigger type (long) 🧮
```c
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    long dollars = 1;
    while (true)
    {
        char c = get_char("Here's $%li. Double it and give to next person? ", dollars);
        if (c == 'y')
        {
            dollars *= 2;
        }
        else
        {
            break;
        }
    }
    printf("Here's $%li.\n", dollars);
}
```
- Why: `long` holds bigger values, delaying overflow (but not preventing it forever).
- Output: larger safe doublings before overflow.

7) Integer division truncates 🔻
```c
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    int x = get_int("What's x? ");
    int y = get_int("What's y? ");
    printf("%i\n", x / y);
}
```
- 7 / 2 → `3` (decimal part discarded).
- When dividing ints, expect truncation.

8) Cast to float for real division 🧊
```c
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    int x = get_int("What's x? ");
    int y = get_int("What's y? ");
    printf("%f\n", (float) x / y);
}
```
- `(float)x` promotes x; result is float, not truncated.
- 7 / 2 → `3.500000`

9) Use floats throughout 🔢
```c
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    float x = get_float("What's x? ");
    float y = get_float("What's y? ");
    printf("%.50f\n", x / y);
}
```
- Shows floating-point precision limits (binary representation can show tiny “noise” digits).
- When to use: need decimals; accept small rounding error.

10) Modulo % (remainder) 🧭
- Pattern: `a % b` gives the remainder after division.
- Examples: `7 % 3 = 1`, `10 % 2 = 0`, `13 % 5 = 3`.
- Use cases: even/odd checks (`n % 2`), cycling through positions, wrapping indices.

11) Quick guidance 🎯
- Pick the right type: `int`/`long` for whole numbers; `float`/`double` when you need fractions.
- Beware overflow: switch to `long` (or bigger types) when values can grow large.
- Beware truncation: cast to float or use float inputs when you need decimals.
- Use `%` for remainder logic.
- Keep format codes matched to types: `%i` int, `%li` long, `%f` float, `%.xf` for precision.
