💬 C (02) - Hello, You

1) Scratch to C
 - In Scratch we could ask “What’s your name?” and say “hello <name>”. In C we can do the same.

2) First attempt (incorrect placeholder)

```c
// get_string and printf with incorrect placeholder

#include <cs50.h>
#include <stdio.h>

int main(void)
{
    string answer = get_string("What's your name? ");
    printf("hello, answer\n");
}
```

 - `get_string` gets a string from the user; `answer` is passed to `printf` (but not correctly yet).
 - Running `make hello` shows errors: `string`/`get_string` need `cs50.h`, and the format is wrong.

3) Correct version (use %s)

```c
// get_string and printf with %s

#include <cs50.h>
#include <stdio.h>

int main(void)
{
    string answer = get_string("What's your name? ");
    printf("hello, %s\n", answer);
}
```

 - `%s` is a format code telling `printf` to expect a string; `answer` fills that placeholder.

4) Run and understand
 - Compile: `make hello`; run: `./hello`. Program asks your name, then says hello with it.
 - `answer` is a variable of type `string`; other types include `int`, `bool`, `char`, etc.
 - `%s` is the placeholder for strings; `answer` is passed to `%s`.
