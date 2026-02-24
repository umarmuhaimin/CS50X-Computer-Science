🧱 C (11) - Mario Blocks

1) Why this example 🧠
- Practice loops, nesting, constants, helper functions with an easy visual.
- Scale from hardcoded output to reusable patterns.

2) Four horizontal blocks (hardcoded) ➡️
```c
#include <stdio.h>

int main(void)
{
    printf("????\n");
}
```
- Output:
```
????
```
- Order: single `printf` runs once; newline at end.

3) Four horizontal blocks (loop) 🔁
```c
#include <stdio.h>

int main(void)
{
    for (int i = 0; i < 4; i++)
    {
        printf("?");
    }
    printf("\n");
}
```
- Why: avoids repetition; easy to change count.
- Output:
```
????
```
- Order: i = 0,1,2,3 print `?`; after loop, print newline.

4) Three vertical blocks 🧱
```c
#include <stdio.h>

int main(void)
{
    for (int i = 0; i < 3; i++)
    {
        printf("#\n");
    }
}
```
- Output:
```
#
#
#
```
- Order: i = 0,1,2; each iteration prints `#` then newline.

5) 3×3 grid (nested loops) 🧊
```c
#include <stdio.h>

int main(void)
{
    for (int i = 0; i < 3; i++)
    {
        for (int j = 0; j < 3; j++)
        {
            printf("#");
        }
        printf("\n");
    }
}
```
- Output:
```
###
###
###
```
- Why nesting: outer = rows, inner = columns.
- Order: outer i=0 → inner j=0,1,2 then newline; outer i=1 → inner again; outer i=2 → inner again. Inner resets each row.

6) 3×3 with a constant 📏
```c
#include <stdio.h>

int main(void)
{
    const int n = 3;
    for (int i = 0; i < n; i++)
    {
        for (int j = 0; j < n; j++)
        {
            printf("#");
        }
        printf("\n");
    }
}
```
- Why const: prevents accidental change; easy to resize by editing one value.
- Order: same as above; outer rows i=0..n-1, inner cols j=0..n-1 per row.

7) Helper function for a row 🛠️
```c
#include <stdio.h>

void print_row(int width);

int main(void)
{
    const int n = 3;
    for (int i = 0; i < n; i++)
    {
        print_row(n);
    }
}

void print_row(int width)
{
    for (int i = 0; i < width; i++)
    {
        printf("#");
    }
    printf("\n");
}
```
- Why: abstraction; row logic in one place; `main` focuses on how many rows.
- Output:
```
###
###
###
```
- Order: `main` outer i=0..n-1 calling `print_row`; inside `print_row`, inner loop i=0..width-1, then newline.

8) When to use which pattern 🎯
- Single loop: 1D output (row or column).
- Nested loops: 2D grids (rows × columns).
- Constants: fixed sizes you don’t want to change accidentally.
- Helper functions: reuse and clarity; split “how many times” (main) from “what to print” (helper).

9) Quick variations to try 🧪
- Change `n` to 4 or 5 to resize the grid.
- Rectangle: outer rows = r, inner cols = c.
- Pyramids: mix spaces and hashes inside the inner loop.
