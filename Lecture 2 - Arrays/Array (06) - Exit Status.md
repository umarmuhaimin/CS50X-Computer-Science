✅ Exit Status (Array 06)

1️⃣ What is an exit status?  
- When a program finishes, it returns a numeric code to the OS.  
- Convention: `0` = success; non‑zero (often `1`) = error.

2️⃣ Example program (status.c)  
```c
// Returns explicit value from main
#include <cs50.h>
#include <stdio.h>

int main(int argc, string argv[])
{
    if (argc != 2)
    {
        printf("Missing command-line argument\n");
        return 1;   // error
    }
    printf("hello, %s\n", argv[1]);
    return 0;       // success
}
```
- Outputs:  
  - `./status David` → prints `hello, David`, exit status `0`.  
  - `./status` (missing arg) → prints `Missing command-line argument`, exit status `1`.
- Why: returning different codes lets scripts or users know if things went right or wrong.
- Clarification: `return` in `main` sets the process exit status.

3️⃣ Checking the exit status in the shell  
- Run a command, then type `echo $?` to see the status of the *previous* command.  
- Useful for chaining commands or debugging scripts.

4️⃣ Practical use  
- Validate inputs (e.g., correct number of arguments) and exit early with a non‑zero status on errors.  
- Downstream tools or shell scripts can branch based on that status.
