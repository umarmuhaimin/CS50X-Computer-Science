🛠️ Compiling (Array 01)

1️⃣ What a compiler does

A compiler is a special program that turns source code → machine code the computer understands.
Today: using a C compiler to do that conversion.

Source code → Compiler → Machine code


2️⃣ Code → machine code example
```c
#include <stdio.h>

int main(void)
{
    printf("hello, world\n");
}

- The compiler translates this into 0s/1s (illustrative):

01010100 01001000 01001001 01010011
00100000 01001001 01010011 00100000
01000011 01010011 00110101 00110000

- Real machine code is much longer.
```


3️⃣ Using clang (in VS Code)
```c
VS Code (CS50) uses clang (“C Language Family Frontend”).
Compile manually: clang -o hello hello.c (flags: -o hello hello.c).
Run: ./hello.
```


4️⃣ Example with CS50 library
```c
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    string name = get_string("What's your name? ");
    printf("hello, %s\n", name);
}
```

- Compile with library: clang -o hello hello.c -lcs50.
- Or simply make hello (runs the full clang command for you).
- Using make in CS50 is expected and convenient.


5️⃣ The 4 compilation stages
```c
Compiling has four steps:

a) Preprocessing – #include headers are copied into your file (e.g., cs50.h, stdio.h).

string get_string(string prompt);
int printf(string format, ...);

int main(void)
{
    string name = get_string("What's your name? ");
    printf("hello, %s\n", name);
}


b) Compiling – C is turned into assembly.

...
main:
    .cfi_startproc
# BB#0:
    pushq    %rbp
.Ltmp0:
    .cfi_def_cfa_offset 16
.Ltmp1:
    .cfi_offset %rbp, -16
    movq    %rsp, %rbp
.Ltmp2:
    .cfi_def_cfa_register %rbp
    subq    $16, %rsp
    xorl    %eax, %eax
    movl    %eax, %edi
    movabsq $.L.str, %rsi
    movb    $0, %al
    callq   get_string
    movabsq $.L.str.1, %rdi
    movq    %rax, -8(%rbp)
    movq    -8(%rbp), %rsi
    movb    $0, %al
    callq   printf
    ...


c) Assembling – assembler converts assembly to machine code (0s and 1s).

01111111010001010100110001000110
00000010000000010000000100000000
00000000000000000000000000000000
00000000000000000000000000000000
00000001000000000011111000000000
00000001000000000000000000000000
00000000000000000000000000000000
...


d) Linking – pre-compiled machine code from libraries is combined with your code to make the final executable.

01111111010001010100110001000110
00000010000000010000000100000000
00000000000000000000000000000000
00000000000000000000000000000000
00000001000000000011111000000000
00000001000000000000000000000000
00000000000000000000000000000000
00000000000000000000000000000000
00000000000000000000000000000000
00000000000000000000000000000000
10100000000000100000000000000000
00000000000000000000000000000000
00000000000000000000000000000000
01000000000000000000000000000000
00000000000000000100000000000000
00001010000000000000000100000000
01010101010010001000100111100101
01001000100000111110110000010000
00110001110000001000100111000111
01001000101111100000000000000000
00000000000000000000000000000000
00000000000000001011000000000000
11101000000000000000000000000000
00000000010010001011111100000000
00000000000000000000000000000000
00000000000000000000000000000000
00000000000000000000000001001000
...
```