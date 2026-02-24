🎬 CS50x Week 1 — Conditional Statements (Doug Lloyd)

1) What are conditional statements?
- “Conditional statements allow your programs to make decisions and take different forks in the road…”
- Decisions can be based on:
  - ✅ the values of variables
  - ✅ what the user inputs (command line or prompt)
- “C has a couple of different ways to express conditional expressions… sometimes called a conditional branch.”


2) if — The simplest conditional
- 🎮 Scratch analogy: hexagon like “if mouse down”, “if x is less than 10”; if true, the code inside executes.
- 💻 C structure: `if (Boolean expression) { ... }`
- ✅ Behavior: if expression is true, run all lines inside; if false, skip them.
  ```c
  if (Boolean expression)
  {
      // lines execute top to bottom if true
  }
  ```


3) if / else — Two paths
- 🎮 Scratch analogy: true branch vs otherwise branch.
- 💻 C version: first block if true, else block if false.
  ```c
  if (Boolean expression)
  {
      // do this if true
  }
  else
  {
      // do this if false
  }
  ```


4) if → else if → else chain (mutually exclusive)
- “You can have if-else if-else if-else… all mutually exclusive; only one branch runs.”
  ```c
  if (expr1) { /* branch 1 */ }
  else if (expr2) { /* branch 2 */ }
  else if (expr3) { /* branch 3 */ }
  else { /* branch 4 */ }
  ```


5) A chain that is NOT mutually exclusive (important concept)
- “It could be that you could satisfy the first condition… and the second… and the third…”
- “The else will only bind to the nearest if.”
- Key idea: else belongs to the closest unmatched if, so some branches can both run if not properly chained.


6) switch statements — discrete cases (not boolean)
- “Neat because it allows you to specify distinct cases, instead of relying on Boolean expressions.”
- Example with breaks:
  ```c
  int x = get_int();
  switch (x)
  {
      case 1: printf("one\n"); break;
      case 2: printf("two\n"); break;
      case 3: printf("three\n"); break;
  }
  ```

- 🛑 Why break matters: without breaks, control “falls through.”
- Intentional fall-through example (countdown):
  ```c
  int x = get_int();
  switch (x)
  {
      case 5: printf("five\n");
      case 4: printf("four\n");
      case 3: printf("three\n");
      case 2: printf("two\n");
      case 1: printf("one\n"); printf("blast off\n");
  }
  ```


7) ?: — the ternary operator
- “Allows you to simulate an If Else with really small… trivially short branches.”
- If/Else version:
  ```c
  if (expression) { x = 5; } else { x = 6; }
  ```

- Equivalent ternary:
  ```c
  int x = expression ? 5 : 6;
  ```

- Read as: expression true? → x=5; else x=6.


8) Quick Summary
- Options in C: if; if-else; if-else if-else; switch (discrete cases); ?: for small if-else.
- Boolean expressions drive conditionals and loops; switch uses discrete values; ternary is a compact if-else.
