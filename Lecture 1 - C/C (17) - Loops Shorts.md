🔁 CS50x Week 1 — Loops in C (Doug Lloyd)

1️⃣ What Are Loops?
- “Loops are kind of cool because they allow your program to execute lines of code over and over and over.”
- They let you: repeat code; avoid copy-pasting; automate repetition.
- 📌 Three major kinds of loops in C: while, do while, for (familiar from Scratch).


2️⃣ Infinite Loop — while (true)
- 🎮 Scratch: forever
- 💻 C: 
  ```c
  while (true)
  {
      // runs forever
  }
  ```
- Runs forever (true is always true).
- 🛑 Stop with break or Control + C.


3️⃣ while Loop (Condition-Controlled)
- Runs while Boolean expression is true; stops when false.
  ```c
  while (Boolean expression)
  {
      // repeat code
  }
  ```
- Example with counter:
  ```c
  int x = 0;
  while (x < 100)
  {
      // do something
      x++;
  }
  ```
- Scratch “repeat until” vs C “while” (inverted logic).


4️⃣ do while Loop (Guaranteed One Run)
- while may run zero times; do while runs at least once.
  ```c
  do
  {
      // run once
  }
  while (Boolean expression);
  ```
- Behavior: run body once, then check; repeat if true.


5️⃣ for Loop (Counter-Controlled)
- 🎮 Scratch: repeat 10
- Basic syntax:
  ```c
  for (int i = 0; i < 10; i++)
  {
      // body
  }
  ```
- Use when repeating a specific number of times.
- Breakdown:
  - start; condition; increment
  - init → check → body → increment → repeat until condition false.
- Example flow:
  ```c
  for (int i = 0; i < 10; i++)
      printf("%i\n", i);
  ```
  - i: 0..9 true, 10 false → stops. Runs exactly 10 times.


6️⃣ Loop Use Cases (When to Use Each)
- 🟢 while: unknown repetitions; might run zero times (game loop, continuous update).
- 🟡 do while: must run at least once (input validation prompt).
  ```c
  do { x = get_int("Positive integer: "); }
  while (x <= 0);
  ```
- 🔵 for: repeat a specific number of times (counting) even if decided at runtime.
  ```c
  int n = get_int("How many times? ");
  for (int i = 0; i < n; i++)
      printf("Hello\n");
  ```


7️⃣ Can Loops Be Interchanged?
- Yes (for ↔ while ↔ do while), but pick the natural one: while (condition), do while (at least once), for (fixed count).

📊 Loop Comparison Table
Loop Type | Runs At Least Once? | Known Iterations? | Common Use
---|---|---|---
while | ❌ No | Unknown | Game loops
do while | ✅ Yes | Unknown | Input validation
for | ❌ No | Known | Counting


🎓 Final Words
- while → repeat while true.
- do while → repeat at least once.
- for → repeat specific number of times.
- Infinite loops need break or Control + C.
