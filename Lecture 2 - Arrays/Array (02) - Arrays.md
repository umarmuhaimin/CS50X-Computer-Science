🧮 Arrays (Array 02)

1️⃣ Data types & memory cost

Common sizes (CS50 env): bool 1 byte, int 4, long 8, float 4, double 8, char 1, string ? (depends on length).
Memory is finite, so type choice matters.


2️⃣ Visualizing memory
A char takes 1 byte; an int takes 4 bytes. Think of each value occupying adjacent “boxes” in RAM.


3️⃣ Hard‑coded average (no arrays yet)
```c
// Averages three (hardcoded) numbers
#include <stdio.h>

int main(void)
{
    int score1 = 72;
    int score2 = 73;
    int score3 = 33;

    printf("Average: %f\n", (score1 + score2 + score3) / 3.0);
}
Uses 3 separate int variables; 3.0 forces float division.
Memory: |72|score1| |73|score2| |33|score3|
```

4️⃣ Why arrays

Arrays store a sequence back‑to‑back in memory.
int scores[3]; asks for 3 adjacent int slots.


5️⃣ Same task with an array
```c
// Averages three (hardcoded) numbers using an array
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    int scores[3];
    scores[0] = 72;
    scores[1] = 73;
    scores[2] = 33;

    printf("Average: %f\n", (scores[0] + scores[1] + scores[2]) / 3.0);
}
scores[0] reads the first element; contiguous layout reduces repetition.
```

6️⃣ Array + loop for input
```c
// Averages three numbers using an array and a loop
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    int scores[3];
    for (int i = 0; i < 3; i++)
    {
        scores[i] = get_int("Score: ");
    }
    printf("Average: %f\n", (scores[0] + scores[1] + scores[2]) / 3.0);
}
scores[i] pairs with loop index to avoid copy‑paste assignments.
```

7️⃣ Abstraction with constant + helper function
```c
// Averages three numbers using an array, a constant, and a helper function
#include <cs50.h>
#include <stdio.h>

const int N = 3;
float average(int length, int array[]);

int main(void)
{
    int scores[N];
    for (int i = 0; i < N; i++)
    {
        scores[i] = get_int("Score: ");
    }
    printf("Average: %f\n", average(N, scores));
}

float average(int length, int array[])
{
    int sum = 0;
    for (int i = 0; i < length; i++)
    {
        sum += array[i];
    }
    return sum / (float) length;
}
```
- N makes the size easy to change.
- average accepts an int array[], showing arrays can be passed between functions.
- Cast to float to avoid integer division.


8️⃣ Key takeaways

Arrays = contiguous storage + index access.
Loops pair naturally with arrays for input/output.
Helper functions and constants keep code flexible and readable.

