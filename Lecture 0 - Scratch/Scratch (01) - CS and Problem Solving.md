🧭 Computer Science and Problem Solving

1) Overview
 - Essentially, computer programming is about taking some input and creating some output - thus solving a problem.
 - What happens in between the input and output, what we could call a black box, is the focus of this course.

2) Black Box Visual
```
[ input ] ---> [ ??? / Algorithm ] ---> [ output ]
```

3) Counting Systems and Bits
 - For example, we may need to take attendance for a class. We could use a system called unary (also called base-1) to count one finger at a time.
 - Computers today count using a system called binary (also called base-2). It’s from the term binary digit that we get a familiar term called bit. A bit is a zero or one: on or off.
 - Computers only speak in terms of zeros and ones. Zeros represent off. Ones represent on. Computers are millions, and perhaps billions, of transistors that are being turned on and off.
 - If you imagine using a light bulb, a single bulb can only count from zero to one.
 - However, if you were to have three light bulbs, there are more options open to you!
 - Inside your devices, such as your iPhone or computer, there are millions of metaphorical light bulbs called transistors that enable the activities conducted on these devices one may take for granted each day.

4) Three-Light-Bulb Place Values
As a heuristic, we could imagine that the following values represent each possible place in our binary digit:

| 4 | 2 | 1 |
|---|---|---|

Using three light bulbs, the following could represent zero:

| 4 | 2 | 1 |
|---|---|---|
| 0 | 0 | 0 |

Similarly, the following would represent one:

| 4 | 2 | 1 |
|---|---|---|
| 0 | 0 | 1 |

By this logic, we could propose that the following equals two:

| 4 | 2 | 1 |
|---|---|---|
| 0 | 1 | 0 |

Extending this logic further, the following represents three:

| 4 | 2 | 1 |
|---|---|---|
| 0 | 1 | 1 |

Four would appear as:

| 4 | 2 | 1 |
|---|---|---|
| 1 | 0 | 0 |

We could, in fact, using only three light bulbs count as high as seven!

| 4 | 2 | 1 |
|---|---|---|
| 1 | 1 | 1 |

5) Bits to Numbers
Computers use base-2 to count. This can be pictured as follows:

| 2^2 | 2^1 | 2^0 |
|-----|-----|-----|
|  4  |  2  |  1  |

Therefore, you could say that it would require three bits (the four’s place, the two’s place, and the one’s place) to represent a number as high as seven.
Similarly, to count a number as high as eight, values would be represented as follows:

| 8 | 4 | 2 | 1 |
|---|---|---|---|
| 1 | 0 | 0 | 0 |

6) Bytes
Computers generally use eight bits (also known as a byte) to represent a number. For example, 00000101 is the number 5 in binary. 11111111 represents the number 255. You can imagine zero as follows:

| 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
|-----|----|----|----|---|---|---|---|
|  0  |  0 |  0 |  0 | 0 | 0 | 0 | 0 |
