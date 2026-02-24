🧠 This is CS50 — Week 1 ~ Section 1 by Kelly Ding.

🎯 Key Questions
- Why are we using C?
- How can we read and write code that includes variables, conditionals, and loops?
- Why do we care about data types?
- What does it mean to compile a C program?


🖥️ Part 0: Using CS50.dev
- Interface: Terminal Window, File Explorer, Text Editor.
- Helpers: Chat with the CS50 Duck / CS50.ai (in codespace), Explain Highlighted Code.
- style50: checks consistency with CS50’s C style guide.
- design50: provides design feedback via CS50.ai.


🔢 Part 1: Variables and Types

1. Input & Printing (Scratch → C)
Scratch: when clicked → say hello, world!
C:
```c
#include <stdio.h>
int main(void)
{
    printf("hello, world\n");
}
```
Output: hello, world
How: include stdio.h for printf; main is entry; newline prints cleanly.
Why: compiler needs prototypes; main returns int status (void params mean none).
Steps: save → make hello → ./hello


2. Using Variables (Scratch → C)
Scratch: set name to David; say join Hello, name
C:
```c
#include <cs50.h>
#include <stdio.h>
int main(void)
{
    string name = "David";
    printf("Hello, %s\n", name);
}
```
Output: Hello, David
Explain: type string, name variable, value "David", %s placeholder matches string.
Why types matter: 01000001 could be int 65 or char 'A' depending on declared type.
Tips: match format codes to types; string needs cs50.h.


3. Variables (components)
Example: int balance = 20;
- type int, name balance, value 20, assignment operator =.
Meaning: “Create an integer named balance that gets the value 20.”
Reassignment: balance = 25; overwrites old value.


4. Operators (start balance=20)
- Add: balance = balance + 1; or balance++; → 21
- Subtract: balance = balance - 1; or balance--; → 19
- Multiply: balance = balance * 2; or balance *= 2; → 40
- Divide: balance = balance / 4; or /=4; → 5 (balance/3 truncates to 6)
- Modulo: balance = balance % 6; or %=6; → 2
Categories: arithmetic + - * / % ; relational < <= == != >= > ; logical && || !
Why: express math/logic; integer division truncates; modulo finds remainders.
Tips: avoid divide by zero; choose operator that matches intent.


5. Getting input / Functions / Printing values
```c
int balance = get_int("Balance: ");
printf("Current Balance: $%i\n", balance);
```
- get_int (from cs50.h) prompts and returns int.
- %i is format code; placeholder value order matters.


6. Types and format codes
- Numbers: int (%i), float (%f)
- Text: char (%c), string (%s)
Why: correct code avoids misprints.

▶️ Running Programs
- ./hello → hello, world
- ./hello (interactive) Adele → hello, Adele; David → hello, David
Steps: make program → run with ./program → answer prompts.

Contacts Exercise
Create contacts.c: ask for name, age, phone number, and a value of your choice; print back as confirmation.
Example:
Name: Mario | Age: 22 | Phone: 929-55-MARIO | Location: Mushroom Kingdom →
“New contact: Mario, 22, lives in Mushroom Kingdom and can be reached at 929-55-MARIO.”


🔁 Part 2: Loops and Conditionals

Conditionals
```c
if (balance < 0)
{
    printf("Insufficient Funds");
}
else
{
    printf("Available Balance");
}
```
- Boolean expression inside if; mutually exclusive paths.
- Output: depends on balance (<0 or not).
- Tip: use == for equality, not =.


1. For Loops
```c
for (int i = 1; i <= 30; i++)
{
    printf("I can count to %i!\n", i);
}
```
- Parts: initialization, Boolean expression, incrementation.
- Output: counts 1..30 on new lines.
- When: known iteration count.


2. While Loops
```c
int i = 1;
while (i <= 30)
{
    printf("I can count to %i!\n", i);
    i = i + 1;
}
```
- Needs initialization; update inside.
- Output: same 1..30.
- When: condition-controlled.


3. Do-While Loops
```c
int n;
do
{
    n = get_int("N: ");
}
while (n <= 0);
```
- Runs body at least once; good for input validation.


🎯 Sum Exercise (sum.c)
- Ask ten integers; print sum.
Example run 1..10 → Sum: 55.
Steps: sum=0; loop 10 times reading get_int; sum += value; printf sum.
Full solution with explanation:
```c
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    int sum = 0;              // start at 0
    for (int i = 0; i < 10; i++)  // loop exactly 10 times
    {
        int n = get_int("Number: "); // prompt each time
        sum += n;                    // add to running total
    }
    printf("Sum: %i\n", sum);    // print the total
}

```
How it works (step by step):

1. Initialize sum to 0 (so you don’t add garbage).
2. for (int i = 0; i < 10; i++) runs the body 10 times (i = 0..9).
3. Inside the loop: prompt with get_int, store in n, then sum += n accumulates the total.
4. After the loop, print the final sum.

Why:
- For loop is ideal because you know you need exactly 10 inputs.
- A running total avoids storing all numbers—memory-light and simple.
- Input is typed (int) so compiler and format code (%i) stay consistent.

Tips:
- Validate input if needed (e.g., reject non-int or negative); you could wrap the prompt in a do-while for stricter rules.
- If you ever want a variable count, read count = get_int("How many?"); then loop count times.
- Always initialize accumulators (sum = 0) and loop counters.
- Match format codes to types (%i here).
- For debugging, you can print partial sums inside the loop.


🎮 Part 3: Mario

Pyramid Output (target)
```
#
##
###
####
#####
######
```
- Left-aligned pyramid; use loops.

Function Breakdown
```c
void print_row(int bricks)
{
    # Print row of bricks
}
```
- Return type void (no value), name print_row, input int bricks.
- Example call print_row 3 → Output ###
- Why: abstraction; reuse row logic; keeps main simple.

Full solution (left-aligned pyramid) with steps
```c
#include <cs50.h>
#include <stdio.h>

void print_row(int bricks);

int main(void)
{
    const int rows = 6;          // target height
    for (int r = 1; r <= rows; r++)
    {
        print_row(r);            // row r has r bricks
    }
}

void print_row(int bricks)
{
    for (int i = 0; i < bricks; i++)
    {
        printf("#");
    }
    printf("\n");
}
```
- Output:
```
#
##
###
####
#####
######
```

🧩 Full step‑by‑step trace in your style:

1st Loop
Main: r = 1; 1 <= 6 → yes → call print_row(1)

print_row(1):

i = 0, 0 < 1 → yes → print #
i = 0+1 = 1
i = 1, 1 < 1 → no → exit inner loop
print newline
return to main
Main: r = 1+1 = 2

2nd Loop
Main: r = 2; 2 <= 6 → yes → call print_row(2)

print_row(2):

i = 0, 0 < 2 → yes → print #
i = 0+1 = 1
i = 1, 1 < 2 → yes → print # (same row)
i = 1+1 = 2
i = 2, 2 < 2 → no → exit inner loop
print newline
return to main
Main: r = 2+1 = 3

3rd Loop
Main: r = 3; 3 <= 6 → yes → call print_row(3)

print_row(3):

i = 0 < 3 → print #; i=1
i = 1 < 3 → print #; i=2
i = 2 < 3 → print #; i=3
i = 3 < 3 → no → exit, newline, return
Main: r = 3+1 = 4

4th Loop
Main: r = 4; 4 <= 6 → yes → print_row(4)
Inner prints 4 hashes (i=0..3), newline, return
Main: r = 4+1 = 5

5th Loop
Main: r = 5; 5 <= 6 → yes → print_row(5)
Inner prints 5 hashes (i=0..4), newline, return
Main: r = 5+1 = 6

6th Loop
Main: r = 6; 6 <= 6 → yes → print_row(6)
Inner prints 6 hashes (i=0..5), newline, return
Main: r = 6+1 = 7

7th check
Main: r = 7; 7 <= 6 → no → outer loop stops → program ends.

- Output:
```
#
##
###
####
#####
######
```

🧩 Here’s the flow, step by step:

Program starts at main.
rows is set to 6 (constant height).
for (int r = 1; r <= rows; r++) runs with r = 1,2,3,4,5,6.
For each r:
• a) Call print_row(r).
• Inside each print_row(r) call:
  - print_row receives bricks = r (the current row number).
  - for (int i = 0; i < bricks; i++) runs; i = 0 to bricks-1 prints one # each time.
  - After the inner loop finishes, printf("\n"); prints a newline to end that row.
  - print_row returns to main.
• Back to main:
  - The loop increments r and repeats until r > rows.
  - When r reaches 7, the for-loop condition fails; main finishes.
• What you see printed (line by line as r increases):
  - r=1 → #
  - r=2 → ##
  - r=3 → ###
  - r=4 → ####
  - r=5 → #####
  - r=6 → ######
• Tips to visualize:
  - Think “outer loop = rows” and “inner loop = bricks per row.”
  - Each call to print_row prints exactly bricks hashes then a newline.
  - Changing rows changes the height; changing print_row logic changes what a row looks like.

🧩 Think of two counters:
• r++ belongs to the OUTER loop: it steps through rows (1, 2, 3, …). Each new r means “start a new row.”
• i++ belongs to the INNER loop inside print_row: it steps through columns/hashes within a single row (0,1,2,… up to bricks-1).
• Flow:
  - Outer loop sets r = 1, calls print_row(1).
  - Inner loop runs i = 0 once → prints one #.
  - Outer increments r++ to 2, calls print_row(2).
  - Inner runs i = 0,1 → prints two #.
  - Outer increments r++ to 3, calls print_row(3).
  - Inner runs i = 0,1,2 → prints three #.
  - … and so on.
  - So: r++ moves to the next row; i++ moves to the next column/hash inside that row. Outer controls “how many rows,” inner controls “how many bricks per row.”

🧩 Inside print_row, i++ happens at the end of each inner-loop iteration. The sequence is:
• Check i < bricks.
• Execute the body (printf("#");).
• Execute i++ (increment).
• Loop back to step 1 and check again.
• So i increases right after printing each #, before the next condition check.

🧩 In print_row:
• i = 0 (initialization)
• i < bricks (Boolean test) --> If pass/meet the condition of boolean test --> print(#), if NO --> exit the loop and print a new line.
• i++ (increment after each iteration)
• Loop body:
  - Check i < bricks; if true, enter body.
  - Print #.
  - Run i++.
  - Go back to the test.
• When the test i < bricks is false, the loop exits and the next line (printf("\n");) runs to move to a new line.



📌 Why this structure: outer loop handles rows; helper handles columns; easy to change height or row logic in one place.

📌 Tips:
  - Use `const` for fixed sizes so you don’t accidentally change them.
  - Start rows at 1 so row number matches brick count.
  - If you need a different height, change `rows`; if you need other symbols, change `printf("#");`.
  - For right-aligned or centered pyramids, add spaces before hashes in `print_row` based on row.

📌 Tips & Reminders
- Include needed headers (stdio.h, cs50.h).
- Initialize variables; C doesn’t auto-set locals.
- Match format codes to types.
- Compile with make; run with ./prog; fix first compiler error first.
- Comment “why,” not obvious “what.”
