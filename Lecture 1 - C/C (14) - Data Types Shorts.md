🎵 CS50x Week 1 – Data Types & Variables
(Data Type Shorts – Structured Notes)

🧠 1️⃣ Why Data Types Matter in C
• Modern languages (PHP, JavaScript, etc.) do not require explicit data types.
• In modern languages:
  - You declare a variable.
  - The language figures out the type automatically.
  - If it’s an integer → it knows.
  - If it’s a string → it knows.
• ⚠️ But C is different.
  - C is an older language.
  - You must explicitly specify the data type the first time you create a variable.
• 👉 Every variable must have:
  - A type
  - A name

🔢 2️⃣ Built-in Data Types in C
🔵 (1) int – Integers
• Used to store: 1, 2, 3; -1, -2, -3 (whole numbers only)
• 🧮 Memory Size: 4 bytes (32 bits)
• 📏 Range: split half negative/half positive → -2^31 to 2^31 - 1 (≈ -2B to +2B)
• 📌 Important: Integers always take 4 bytes (32 bits).

🟢 unsigned int
• Not a separate type — it is a qualifier.
• What it does: removes negative values; doubles positive range.
• Range: 0 to 2^32 - 1 (≈ 0 to 4 billion).
• ⚖️ Trade-off: larger positive range; no negative numbers allowed.

🔎 Other Qualifiers (Not Covered Deeply)
• short, long, const
• We mostly focus on: unsigned

🟡 (2) char – Characters
• Used to store: single characters 'A' '0' '!'
• 🧠 Memory Size: 1 byte (8 bits)
• 📏 Range: -2^7 to 2^7 - 1 (−128 to 127)
• 🔤 ASCII Mapping: numbers map to chars (e.g., 'A'→65, 'a'→97, '0'→48)
• ⚠ '0' (character) ≠ 0 (number)

🔵 (3) float – Floating Point Numbers
• Used to store: real numbers, decimals (e.g., 3.14159)
• 🧠 Memory Size: 4 bytes (32 bits)
• ⚠ Precision Problem: 32 bits split between integer and decimal parts; large integers reduce decimal precision; can’t store 100+ exact decimal digits.

🟣 (4) double – Double Precision Floating Point
• Used to store: real numbers, higher precision decimals
• 🧠 Memory Size: 8 bytes (64 bits)
• ✔ More precise than float; can store many more decimal places
• Use double when: high precision/long decimals/scientific calcs needed

⚫ (5) void – Special Type
• ⚠ Not a data type for variables; cannot do `void x;`
• 📌 Used in Functions:
  - Return type: `void function_name()` means returns nothing (e.g., printf())
  - Parameter list: `int main(void)` means main takes no arguments
• 👉 void = nothing.

📚 3️⃣ CS50 Library Data Types (#include <cs50.h>)
• CS50 provides additional types:
  - 🟢 bool (Boolean: true/false). Not built into C originally; include cs50.h or get errors.
  - 🟣 string (words/sentences/characters). Example: string name; must include cs50.h.

🏗 4️⃣ Creating Variables in C
• To create a variable: specify type, name, semicolon.
• Example: `int number;` `char letter;` (number holds integers, letter holds characters)
• Multiple of same type: `int height, width;` (cleaner) or `float square_root2, square_root3, pi;`
• 🎯 Best Practice: declare when needed; don’t declare everything at top (scope topic later).

🔄 5️⃣ Assigning Values
• Step 1: Declaration `int number;` (creates variable)
• Step 2: Assignment `number = 17;` (now holds 17)
• Another: `char letter; letter = 'H';`
• 🚀 Initialization: `int number = 17;` (declare + assign) ; `char letter = 'H';`
• ⚠ Important: don’t redeclare: `int number = 17; int number = 20;` is wrong; instead `number = 20;` Redeclaration causes issues.

🧩 Summary of Built-In Types
Type | Size | Purpose
int | 4 bytes | Whole numbers
unsigned int | 4 bytes | Positive whole numbers
char | 1 byte | Single character
float | 4 bytes | Decimal numbers
double | 8 bytes | High precision decimals
void | — | No value

📚 CS50 Types
Type | Purpose
bool | true / false
string | collection of characters

🧠 Core Takeaways
- C requires explicit data types.
- Memory size determines range.
- Float has precision limits.
- Double improves precision.
- Void means nothing.
- bool and string require CS50.h.
- Declare once, assign later.
- Don’t redeclare variables.

🎓 Final Thought
- C forces you to understand: Memory, Bits, Precision, Type safety. This is why CS50 teaches it first.
