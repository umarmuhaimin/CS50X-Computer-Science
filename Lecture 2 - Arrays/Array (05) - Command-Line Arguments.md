💻 Command-Line Arguments (Array 05)

1️⃣ What they are  
- Command-line arguments are values you pass *when launching* a program (e.g., the words after `clang` or after `./program`). Your code can read them via `argc` and `argv`.

2️⃣ Baseline: prompt at runtime  
```c
// Uses get_string
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    string answer = get_string("What's your name? ");
    printf("hello, %s\n", answer);
}
```
- Output: asks for your name, then prints `hello, <name>`.  
- Why: input happens *after* the program starts.  
- Clarification: `get_string` waits for the user to type.

3️⃣ Reading an argument passed before run  
```c
// Prints a command-line argument
#include <cs50.h>
#include <stdio.h>

int main(int argc, string argv[])
{
    if (argc == 2)
    {
        printf("hello, %s\n", argv[1]);
    }
    else
    {
        printf("hello, world\n");
    }
}
```
- Output examples:  
  - `./greet David` → `hello, David`  
  - `./greet` (no extra arg) → `hello, world`
- Why: `argc` counts arguments; `argv` is an array of strings holding them.  
- Clarification: `argv[0]` is the program name; first real argument is `argv[1]`.

4️⃣ Printing all arguments  
```c
// Prints command-line arguments
#include <cs50.h>
#include <stdio.h>

int main(int argc, string argv[])
{
    for (int i = 0; i < argc; i++)
    {
        printf("%s\n", argv[i]);
    }
}
```
- Output for `./args one two`:
  ```
  ./args
  one
  two
  ```
- Why: loops over every argument using `argc` as the limit.  
- Clarification: `argv[i]` is a string; loop includes index 0 (program name) by design.
