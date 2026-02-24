🧾 C (13) - Summary

1) Big picture
- We mapped Scratch concepts (functions, conditionals, loops, variables) into C.
- Wrote, compiled, and ran C programs from the CLI.

2) Core skills covered
- First C program (`hello`): edit → `make` → `./run`.
- Command line basics: `ls`, `cd`, `make`, running executables.
- Using standard headers (`stdio.h`) and CS50 helpers (`cs50.h`).
- Variables and types: int, long, float, char, string; choosing the right one.
- Operators: `+ - * / %`, casting, overflow awareness, integer vs float division.
- Conditionals: if / else if / else, comparisons, logical OR.
- Loops: while, do-while, for; input validation patterns; nested loops for grids.
- Functions: prototypes, parameters, return values, scope, helper functions.
- Design helpers: constants, helper functions for reuse and clarity.

3) Quality checks
- Correctness: pass the tests (`check50`, your own edge cases).
- Design: avoid repetition, clear structure, good data types (`design50` where available).
- Style: readable, consistent formatting (`style50`).

4) Notes on pitfalls
- Overflow with ints; use `long` when growth is large.
- Integer division truncates; cast or use floats for decimals.
- Floating-point precision limits; small “noise” is normal.
- Match format codes to types: `%i` int, `%li` long, `%f` float, `%.xf` for precision, `%s` string, `%c` char.

5) Next steps
- Practice by tweaking loop counts, grid sizes, and function parameters.
- Re-run quality tools before submitting assignments.
- Keep building: more data structures and memory concepts come next.


