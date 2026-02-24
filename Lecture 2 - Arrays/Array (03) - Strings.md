🔤 Strings (Array 03)

1️⃣ What is a string?  
- A string is just an **array of char** values—characters stored back‑to‑back.

2️⃣ Print characters (hi.c)  
```c
// Prints chars
#include <stdio.h>

int main(void)
{
    char c1 = 'H';
    char c2 = 'I';
    char c3 = '!';
    printf("%c%c%c\n", c1, c2, c3);
}
```
- Output: `HI!` — showing three chars next to each other.

3️⃣ Print ASCII codes  
```c
// Prints chars' ASCII codes
#include <stdio.h>

int main(void)
{
    char c1 = 'H';
    char c2 = 'I';
    char c3 = '!';
    printf("%i %i %i\n", c1, c2, c3);
}
```
- Switching `%c` → `%i` shows their ASCII numbers.  
- Output: `72 73 33`

4️⃣ How strings live in memory  
- A string starts at index 0 and ends with a special **NUL (`'\0'`)** terminator (one “L”; different from `NULL`).  
- Example layout: `H s[0] | I s[1] | ! s[2] | \0 s[3]`  
- Same in decimal: `72 | 73 | 33 | 0`

5️⃣ Print a whole string  
```c
// Prints string
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    string s = "HI!";
    printf("%s\n", s);
}
```
- `%s` prints all chars until `\0`.  
- Output: `HI!`

6️⃣ Treat string as an array  
```c
// Treats string as array
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    string s = "HI!";
    printf("%c%c%c\n", s[0], s[1], s[2]);
}
```
- Indexing (`s[0]`, `s[1]`, `s[2]`) pulls individual chars.  
- Output: `HI!`

7️⃣ ASCII codes including NUL  
```c
// Prints string's ASCII codes, including NUL
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    string s = "HI!";
    printf("%i %i %i %i\n", s[0], s[1], s[2], s[3]);
}
```
- Shows the hidden terminator too.  
- Output: `72 73 33 0`

8️⃣ Two separate strings  
```c
// Multiple strings
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    string s = "HI!";
    string t = "BYE!";
    printf("%s\n", s);
    printf("%s\n", t);
}
```
- Two variables; each holds its own char array.  
- Visual: `H|I|!|\0` then `B|Y|E|!|\0`.
- Output:  
  ```
  HI!
  BYE!
  ```

9️⃣ Array of strings  
```c
// Array of strings
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    string words[2];
    words[0] = "HI!";
    words[1] = "BYE!";

    printf("%s\n", words[0]);
    printf("%s\n", words[1]);
}
```
- Stores multiple strings in one array of type `string`.
- Output:  
  ```
  HI!
  BYE!
  ```

🔟 Accessing chars inside an array of strings  
```c
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    string words[2];
    words[0] = "HI!";
    words[1] = "BYE!";

    printf("%c%c%c\n", words[0][0], words[0][1], words[0][2]);
    printf("%c%c%c%c\n", words[1][0], words[1][1], words[1][2], words[1][3]);
}
```
- `words[?][?]` = choose the word, then the character inside it.  
- Result: first line prints `HI!`; second prints `BYE!`.
