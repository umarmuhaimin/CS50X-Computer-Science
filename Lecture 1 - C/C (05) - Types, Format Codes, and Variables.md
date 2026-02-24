🔢 C (05) - Types, Format Codes, and Variables

1) Types (common)
 - bool
 - char
 - float
 - int
 - long
 - string
 - … (more exist, but these are core for now)
 - bool: true or false
 - char: one character (like 'A')
 - float: number with decimals (approximate)
 - int / long: whole numbers (long can store bigger ones)
 - string: text (many characters)

2) Format codes (printf placeholders)
 - `%c`  char
 - `%f`  float (floating-point)
 - `%i`  int (integer)
 - `%li` long int
 - `%s`  string
 - Use these with `printf`; see Manual Pages for details.
 - Float precision: `%.2f` shows 2 decimal places; `%.1f` shows 1.
 - Match each placeholder to the right type; otherwise output will be wrong.

3) Variables (int examples)
```c
int counter = 0;     // declare and initialize
counter = counter + 1; // add one
counter += 1;        // add one (shorthand)
counter++;           // add one (increment)
counter--;           // subtract one (decrement)
```
 - `counter` is type `int`; `++` adds 1; `--` subtracts 1.
 - Pick the smallest type that fits: `int` for small counts, `long` for bigger counts, `float`/`double` for decimals, `char` for one letter, `string` for text.
