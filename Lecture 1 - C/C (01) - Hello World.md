💻 C (01) - Hello World

1) Commands to write/compile/run
 - `code hello.c`
 - `make hello`
 - `./hello`
 - `code hello.c` creates the file so we can type instructions. `make hello` compiles the C file into an executable named `hello`. `./hello` runs that program.

2) First C program
Type `code hello.c` in the terminal, then write:

```c
// A program that says hello to the world

#include <stdio.h>

int main(void)
{
    printf("hello, world\n");
}
```

 - Every character matters: `printf` outputs text; quotes and semicolon are required; `\n` makes a new line after “hello, world”.
 - Compile with `make hello` (omit `.c`). If no errors, run `./hello` to see the output.
 - In the file explorer you should see `hello.c` (source) and `hello` (executable machine code).

3) From Scratch to C
 - In Scratch we used the `say` block; in C we call `printf`.

```c
printf("hello, world\n");
```

 - The argument `"hello, world\n"` is in double quotes; the line ends with `;`.

4) Common syntax slip: missing `\n`

```c
// \n is missing
#include <stdio.h>

int main(void)
{
    printf("hello, world");
}
```

 - Recompile with `make hello`; then run `./hello` to see the difference (no line break). `\` is an escape character; `\n` means newline.
 - Other escape sequences:
   - `\n` new line
   - `\r` return to start of line
   - `\"` print a double quote
   - `\'` print a single quote
   - `\\` print a backslash

Restore after experimenting:

```c
// A program that says hello to the world

#include <stdio.h>

int main(void)
{
    printf("hello, world\n");
}
```

5) Header files and manuals
 - `#include <stdio.h>` tells the compiler to pull in the `stdio.h` library (not “studio”) so `printf` works.
 - A library is a collection of pre-written code/functions we can reuse.
 - Manual Pages document library functions and commands.
 - CS50 provides `cs50.h` with training-wheels functions:
   - `get_char`, `get_double`, `get_float`, `get_int`, `get_long`, `get_string`
 - These are pre-installed at cs50.dev; on your own machine you’d need to install them—another reason to use cs50.dev.

6) Next step
 - Let’s use this library in your program.
