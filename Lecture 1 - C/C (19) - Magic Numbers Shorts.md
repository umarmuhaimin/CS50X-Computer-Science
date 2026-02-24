🪄 CS50x Week 1 — Magic Numbers (Doug Lloyd)

1) What are “magic numbers”?
- “Kind of a strange topic, right? Magic numbers.”
- “Some of the programs that we’ve written in CS50 so far have had some weird numbers kind of thrown in them.”
- These are constants written directly into code without obvious meaning.
- ✅ Why that’s bad: “In general, it’s kind of a bad habit actually to write constants into your code.” “it’s sometimes referred to as using magic numbers” “which is something we generally want to try and avoid.”

2) Example from Mario: Why 23?
- “In the Mario problem, we capped the height of the pyramid at 23.”
- Reason: “the standard height of a terminal window is 24.” “if we have the pyramid be taller than that, it might do this weird thing where it runs off the screen.”
- ⚠ Problem: “Is the meaning of 23 immediately obvious to somebody who looks at your program…? Probably not.” “It seems like… why is it just less than 23?”
```c
#include <cs50.h>
#include <stdio.h>

#define MAX_HEIGHT 23  // keep pyramid on screen (~24 rows)

int main(void)
{
    int h;
    do
    {
        h = get_int("Height (1-%i): ", MAX_HEIGHT);
    }
    while (h < 1 || h > MAX_HEIGHT);

    for (int r = 1; r <= h; r++)
    {
        for (int c = 0; c < r; c++)
        {
            printf("#");
        }
        printf("\n");
    }
}
```

3) Deck of cards example: hidden constants
- Doug’s pseudocode: deal_card(deck) returns one card; loop 0 to 52.
- Magic number: 52 “just kind of floating around.”
- Even if you know a deck has 52 cards, in a big codebase it’s unclear.

4) First “fix”: store 52 in a variable (better, but risky)
- Example idea: `int deck_size = 52; for (int i = 0; i < deck_size; i++) { /* deal */ }`
- ✅ Improves symbolic meaning.
- ⚠ Risk: another function could change deck_size → wrong count, exploitable.
```c
#include <stdio.h>

int deal_card(int i) { return i; } // placeholder

int main(void)
{
    int deck_size = 52;
    for (int i = 0; i < deck_size; i++)
    {
        deal_card(i);
    }
}
```

5) Best solution: #define (symbolic constants)
- “C provides what’s called a preprocessor directive, which is also called a macro for creating symbolic constants.”
- “You’ve already seen a preprocessor directive… with #include.”
- ✅ Syntax: “#define, name, replacement.” “Don’t put a semicolon at the end of your #defines.”
  ```c
  #define NAME REPLACEMENT
  ```
- 🔍 Compile time: “the compiler is going to go through your code and replace every instance of the word ‘name’ with… replacement.”
- Analogy: “If #include is sort of similar to copying and pasting… then #define is sort of similar to find and replace…”

6) Example: defining PI
- “If I #define pi as 3.14159265…”
- `#define PI 3.14159265`
- Benefit: use PI, not long digits; compiler substitutes number.
```c
#include <stdio.h>

#define PI 3.14159265

int main(void)
{
    double r = 2.5;
    double area = PI * r * r;
    printf("Area = %.5f\n", area);
}
```

7) #define is not only for numbers
- Example: `#define COURSE "CS50"` — replace COURSE with the string CS50.
```c
#include <stdio.h>

#define COURSE "CS50"

int main(void)
{
    printf("Welcome to %s!\n", COURSE);
}
```

8) Convention: ALL CAPS
- “I frequently #define… constants… always in all caps.”
- “It’s a convention. It’s not required.”
- Reason: “to make it really clear that this… is a defined constant.” lowercase might be confused with a variable.

9) Why #define is safer than variables
- “You can’t manipulate a constant.” “You can’t say 52++.”
- Example: `#define DECK_SIZE 52` then `for (int i = 0; i < DECK_SIZE; i++) { ... }`
- Prevents accidental change of the value compared to a variable like deck_size.
```c
#include <stdio.h>

#define DECK_SIZE 52

int deal_card(int i) { return i; } // placeholder

int main(void)
{
    for (int i = 0; i < DECK_SIZE; i++)
    {
        deal_card(i);
    }
}
```

🔟 Bonus benefit: easy to “port”
- Different decks elsewhere (e.g., Germany 32 cards). With #define, change one line: `#define DECK_SIZE 32` and recompile; it propagates everywhere.
```c
#define DECK_SIZE 32
```
