🧭 C (10) - Correctness, Design, Style

1) The three axes
 - Correctness: Does the code do the right thing? (passes tests, handles edge cases, no crashes)
 - Design: Is the solution well-structured? (no unnecessary work, clear logic, good function choices)
 - Style: Is the code clean and consistent? (spacing, naming, comments, follows course style guide)

2) Why they matter
 - Correctness is mandatory—wrong answers fail programs.
 - Good design makes code faster, safer, and easier to change.
 - Good style makes code readable for you and teammates; fewer bugs slip in.

3) How to check
 - `check50` : automated correctness tests for many psets.
 - `design50`: checks for efficiency/structure on supported problems.
 - `style50` : checks formatting/style against CS50’s rules.

4) When to run them
 - After you think the program works: run `check50` to verify.
 - Once logic feels solid: run `design50` if available to spot design issues.
 - Before submitting: run `style50` to clean up formatting and catch style slips.

5) Tips for passing each axis
 - Correctness: test edge inputs (0, negatives, max/min), handle errors, avoid uninitialized variables.
 - Design: remove duplicate code (use functions), choose right data types/structures, avoid needless loops/IO.
 - Style: meaningful variable names, consistent indentation, spaces after commas, clear comments only where helpful.
