🔢 String Length (Array 04)

1️⃣ Why we care  
- We often need a string’s length. Let’s implement it step by step.

2️⃣ Manual counting (length.c)  
```c
// Determines the length of a string
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    string name = get_string("Name: ");
    int n = 0;
    while (name[n] != '\0')  // count until NUL
    {
        n++;
    }
    printf("%i\n", n);
}
```
- Loops until the NUL terminator.  
- Output: prints the character count of the input name.
- Why: `\0` marks the end of every C string, so walking until `\0` tells us how many characters are stored.
- Clarification: `n` is an index counter; it increases one per character until it reaches the terminator.

3️⃣ Extract the counting into a helper  
```c
// Determines the length of a string using a function
#include <cs50.h>
#include <stdio.h>

int string_length(string s);

int main(void)
{
    string name = get_string("Name: ");
    int length = string_length(name);
    printf("%i\n", length);
}

int string_length(string s)
{
    int n = 0;
    while (s[n] != '\0')
    {
        n++;
    }
    return n;
}
```
- `string_length` handles the loop; main stays clean.  
- Output: same length value printed.
- Why: moving the loop into `string_length` lets you reuse it anywhere, keeping `main` shorter.
- Clarification: the function returns an `int` length; `string` is just `char*` under the hood, so indexing works the same.

4️⃣ Using the standard library (`strlen`)  
```c
// Determines the length of a string using string.h
#include <cs50.h>
#include <stdio.h>
#include <string.h>

int main(void)
{
    string name = get_string("Name: ");
    int length = strlen(name);
    printf("%i\n", length);
}
```
- `strlen` from `string.h` does the counting for you.  
- Output: length of the input string.
- Why: library code is tested and faster; no need to rewrite common logic.
- Clarification: `strlen` stops at the first `\0`, just like our manual loop.

5️⃣ Uppercasing manually (no ctype yet)  
```c
// Uppercases a string (manual ASCII math)
#include <cs50.h>
#include <stdio.h>
#include <string.h>

int main(void)
{
    string s = get_string("Before: ");
    printf("After:  ");
    for (int i = 0, n = strlen(s); i < n; i++)
    {
        if (s[i] >= 'a' && s[i] <= 'z')
        {
            printf("%c", s[i] - 32);
        }
        else
        {
            printf("%c", s[i]);
        }
    }
    printf("\n");
}
```
- Checks each char; subtracts 32 to turn lowercase ASCII into uppercase.  
- Output: uppercase version printed.
- Why: in ASCII, lowercase and uppercase letters are 32 apart, so subtracting 32 maps a–z to A–Z.
- Clarification: the `if` guard avoids changing characters that aren’t lowercase letters.

6️⃣ ASCII table reminder  
- Lowercase letters become uppercase by subtracting 32 in ASCII.  
- (Works for a–z ASCII only; not for accented/non-ASCII chars.)  
- Reference table (0–127):
```
0	NUL	16	DLE	32	SP	48	0	64	@	80	P	96	`	112	p
1	SOH	17	DC1	33	!	49	1	65	A	81	Q	97	a	113	q
2	STX	18	DC2	34	”	50	2	66	B	82	R	98	b	114	r
3	ETX	19	DC3	35	#	51	3	67	C	83	S	99	c	115	s
4	EOT	20	DC4	36	$	52	4	68	D	84	T	100	d	116	t
5	ENQ	21	NAK	37	%	53	5	69	E	85	U	101	e	117	u
6	ACK	22	SYN	38	&	54	6	70	F	86	V	102	f	118	v
7	BEL	23	ETB	39	’	55	7	71	G	87	W	103	g	119	w
8	BS	24	CAN	40	(	56	8	72	H	88	X	104	h	120	x
9	HT	25	EM	41	)	57	9	73	I	89	Y	105	i	121	y
10	LF	26	SUB	42	*	58	:	74	J	90	Z	106	j	122	z
11	VT	27	ESC	43	+	59	;	75	K	91	[	107	k	123	{
12	FF	28	FS	44	,	60	<	76	L	92	\\	108	l	124	|
13	CR	29	GS	45	-	61	=	77	M	93	]	109	m	125	}
14	SO	30	RS	46	.	62	>	78	N	94	^	110	n	126	~
15	SI	31	US	47	/	63	?	79	O	95	_	111	o	127	DEL
```

7️⃣ Using `ctype.h` (cleaner)  
```c
// Uppercases string using ctype library (and an unnecessary condition)
#include <cs50.h>
#include <ctype.h>
#include <stdio.h>
#include <string.h>

int main(void)
{
    string s = get_string("Before: ");
    printf("After:  ");
    for (int i = 0, n = strlen(s); i < n; i++)
    {
        if (islower(s[i]))
        {
            printf("%c", toupper(s[i]));
        }
        else
        {
            printf("%c", s[i]);
        }
    }
    printf("\n");
}
```
- `islower` checks; `toupper` converts when needed.  
- Output: uppercased string printed.
- Why: `ctype.h` helpers make the intent clear and avoid manual ASCII math.
- Clarification: `islower` returns nonzero for lowercase letters; `toupper` leaves non-letters unchanged.

8️⃣ Even simpler with `toupper` alone  
```c
// Uppercases string using ctype library
#include <cs50.h>
#include <ctype.h>
#include <stdio.h>
#include <string.h>

int main(void)
{
    string s = get_string("Before: ");
    printf("After:  ");
    for (int i = 0, n = strlen(s); i < n; i++)
    {
        printf("%c", toupper(s[i]));
    }
    printf("\n");
}
```
- `toupper` already ignores non-lowercase chars.  
- Output: uppercased string printed.
- Why: `toupper` safely returns the same character if no change is needed, so the condition can be dropped.
- Clarification: loop still uses `strlen(s)` cached in `n` to avoid recomputing length each iteration.

9️⃣ Takeaway  
- You can count length manually, with a helper, or rely on `strlen`.  
- Manual ASCII math works, but `ctype.h` (`toupper`, `islower`) is safer and simpler.
- Clarification: prefer library functions for readability and fewer bugs; fall back to manual only when learning or in constrained environments.
