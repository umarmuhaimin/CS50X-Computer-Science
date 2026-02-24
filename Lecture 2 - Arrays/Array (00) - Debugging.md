📚 Array (00) — Debugging

Introduction
- Last week we learned C as a text-based programming language.
- This week we’ll dig into more building blocks to strengthen bottom‑up programming skills.
- The course is really about problem-solving, so we’ll keep sharpening how to approach CS problems.
- By the end, you’ll combine these building blocks to solve many CS challenges.
- We often take these solutions for granted; now we’ll see how they’re built.

Reading levels problem (real‑world tie‑in)
- One task we’ll tackle is measuring reading levels.
- With help from peers, we looked at texts at different reading levels.
- This week you’ll quantify reading levels as one of the programming challenges.

1) Debugging basics 🪲
- Everyone will make mistakes while coding.
- Debugging is the process of locating and removing bugs from your code.
- Rubber duck debugging: talk through your code to an object (or yourself) to reason about issues. If you prefer, talk to a human.
- Tools: CS50 Duck and CS50.ai can help you debug your code.

2) Missing headers / typos 🧾
// Missing #include for stdio.h
```c
int main(void)
{
    printf("hello, world\n");
}
```
- Missing stdio.h, so printf is unknown to the compiler.

// Misspelled stdio.h
```c
#include <studio.h>

int main(void)
{
    printf("hello, world\n");
}
```
- Typo: studio.h vs stdio.h → compiler can’t find the header.

3) Missing types / library / semicolon / format 🧑‍💻
// Missing cs50.h, variable's type, semicolon, %s, and second printf argument.
```c
#include <stdio.h>

int main(void)
{
    name = get_string("What's your name? ")
    printf("hello, world\n");
}
```
- Errors: no type for name; missing cs50.h for string; missing semicolon; printf not using name.

4) Logical vs compiler errors 🧠
- Some bugs prompt an error message; others are logical (no message but wrong behavior).
- printf can help debug by printing state.

5) Buggy loop example (prints 4 instead of 3) 🧱
```c
#include <stdio.h>

int main(void)
{
    for (int i = 0; i <= 3; i++)
    {
        printf("#\n");
    }
}
```
- Outputs four blocks; intended three.

6) Instrument with printf 🔍
```c
#include <stdio.h>

int main(void)
{
    for (int i = 0; i <= 3; i++)
    {
        printf("i is %i\n", i);
        printf("#\n");
    }
}
```
- Shows i values 0..3 → reveals off-by-one.

7) Fix off-by-one ✅
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
- Change <= to <.

8) Another buggy example for debug50 🛠️
```c
#include <cs50.h>
#include <stdio.h>

void print_column(int height);

int main(void)
{
    int h = get_int("Height: ");
    print_column(h);
}

void print_column(int height)
{
    for (int i = 0; i <= height; i++)
    {
        printf("#\n");
    }
}
```
- Still buggy (off-by-one).

9) Using debug50 (VS Code debugger) 🐞
- Set a breakpoint (click left of line number → red dot).
- Run `debug50 ./buggy`; code pauses at highlight (gold color).
- View locals (e.g., h uninitialized), step over to watch i increase.
- Debugger helps you slow down and see execution step by step; step into for deeper inspection.

10) Rubber duck (or human) 🦆
- Talk through the problem and steps aloud to find errors.
- CS50 Duck can help with debugging your code.
