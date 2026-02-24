🎵 CS50x Week 1 — Operators in C (Doug Lloyd)

1) What are operators?
- ✅ Operators help us manipulate and work with values and variables in C.
- 🟰 We’ve already seen one operator: = (assignment operator) → “It allows us to just put a value into a variable.” “That’s the assignment operator, single equal sign.”


2) Arithmetic Operators
- 🧮 Basic math operators — “We can add, subtract, multiply, and divide numbers using plus, minus, star, and slash, respectively.”

Operation | Symbol
---|---
Add | +
Subtract | -
Multiply | *
Divide | /

- 🧪 Example: stepping through code
  - ✅ Example 1 — “So, we have int x equals y plus 1.” “Let’s assume that somewhere up above this line of code we had said int y equals 10.” “What’s the value of x after I execute this first line of code?” “Did you say 11? You’d right.”
    ```c
    int y = 10;
    int x = y + 1;   // x is now 11
    ```

  - ✅ Example 2 — “How about this next line of code?” “x equals x times 5.” “Before we executed this line of code, x was 11.” “So, x equals 11 times 5. Or 55.”
    ```c
    x = x * 5;       // x is now 55
    ```
- 🔁 Key rule about assignment — “The value on the right gets assigned to the value on the left.” “First we evaluate x times 5.” “And then we store that value in x.” “The 11 that was there before is now overwritten.”


3) Modulus Operator (Remainder)
- 🧠 What it does — “It’s called the modulus operator.” “What modulus does is it gives you the remainder when you divide two numbers together.”

- 🧪 Example: 13 % 4 — “So, I have a line of code here, int m equals 13 mod 4.” “m’s value is now 1.” “4 goes into 13 three times with a remainder of 1.”
  ```c
  int m = 13 % 4;   // m is now 1
  ```

- 🔥 Why it’s useful — “You might think that’s actually not a terribly useful thing, but you’d be surprised…” “There’s a couple of problems we’ll do CS50 that deal with it.”

- ✅ Example use case: random number “whittling down” — “Random number generator gives you a number from 0 to some huge number.” “But maybe you only really need a number from 0 to 20.” “Divide it by 20 and get the remainder.” “The remainder can only be a value from 0 to 19.”
  ```c
  random_value % 20;   // yields 0–19
  ```


4) Shorthand Arithmetic Assignment
- 🧠 The idea — “C also gives us a way to apply an arithmetic operator to a single variable in a little more shorthand way.”
- Instead of: `x = x * 5;`
- You can write: `x *= 5;`
- “It’s the same exact thing… just a slightly shorter way to do it.”
- ✅ Works with multiple operators — “You could say x plus equals 5, minus equals 5, times, divide, and mod.”
  ```c
  x += 5;
  x -= 5;
  x *= 5;
  x /= 5;
  x %= 5;
  ```


5) Increment & Decrement
- 🔥 Why they exist — “Incrementing a variable by 1 or decrementing a variable by 1 is such a common thing…” “Especially when we talk about loops…”
- These all do the same thing: “x equals x plus 1, x plus equals 1, and x plus plus all do the same thing.”
  ```c
  x = x + 1;
  x += 1;
  x++;
  ```
- Similarly for decrement:
  ```c
  x = x - 1;
  x -= 1;
  x--;
  ```


6) Boolean Expressions (Operators for comparing)
- 🧠 What they are — “Boolean expressions… are used for comparing values.” “All Boolean expressions in C evaluate to one of two possible values… true or false.”
- 🧭 Why they matter — Conditional branching: “If some condition is true, maybe I’ll take this branch… a conditional.” Repetition: “As long as this is true, I want to keep doing this over and over… a loop.”
- 🟦 Boolean values in C (important detail) — “In C, every non-0 value is the same as saying true.” “0, on the other hand, is false.”
- ✅ Implication: “We don’t always have to use Boolean type variables when we are working with Boolean expressions.”


7) Two groups of Boolean operators
- “There are two main types of Boolean expressions…”
  - Logical operators
  - Relational operators
- “Don’t worry about necessarily memorizing the term…”


8) Logical Operators (AND / OR / NOT)

- 🔥 Logical AND: && — “Logical AND is true, if and only if both operands are true. Otherwise false.” Example form: “if x && y” Truth behavior: True only when x is true AND y is true. If either is false → expression is false.
  ```c
  if (x && y)
  {
      // runs only if BOTH are true
  }
  ```

- 🔥 Logical OR: || — “Logical OR is true if and only if at least one operand is true. Otherwise false.” “Requires x to be true or y to be true or both…”
  ```c
  if (x || y)
  {
      // runs if AT LEAST ONE is true
  }
  ```
- ⌨️ Keyboard note (from transcript) — “It’s called the vertical bar.” “Usually just above the Enter key… same key as the backslash.” “Right next to the square brackets.”

- 🔥 Logical NOT: ! — “NOT… inverts the value of its operand.” “If x is true, then not x is false.” “If x is false, then not x is true.” “Sometimes you’ll hear this symbol pronounced as bang or exclamation or not.”
  ```c
  if (!x)
  {
      // runs if x is false (0)
  }
  ```


9) Relational Operators (<, <=, >, >=, ==, !=)
- 🧠 General idea — “These behave exactly as you’d expect.”

Comparison | Meaning | Example
---|---|---
< | True if x is less than y | x < y
<= | True if x is less than or equal to y | x <= y
> | True if x is greater than y | x > y
>= | True if x is greater than or equal to y | x >= y
== | True if x equals y | x == y (“x equals equals y is true if x and y’s value is the same.”)
!= | True if x not equal y | x != y (Uses NOT (bang) + equals “If x is not equal to y…”) 


10) Common Mistake: = vs ==
- “Be really careful here.” “Accidentally mistake the assignment operator, single equals, for the equality comparison operator, double equals.” “It’ll lead to some weird behavior…” “Make sure to use equals equals, and not single equals.”
- ✅ Rule: Use = to assign; Use == to compare


11) Operator Quick Sheet (CS50-style)
- 🧮 Arithmetic — + - * / %
- 🧠 Relational — < <= == != >= >
- 🔗 Logical — && || !
