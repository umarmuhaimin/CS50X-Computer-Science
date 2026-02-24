📋 Debugging with debug50 — "Step Through" (Short by Carter @ CS50)

This short walks through using debug50's **Step Over** feature to trace code line by line. Instead of staring at code and guessing what's wrong, you can watch your variables change in real time as each line executes — a far more powerful technique.

---

## 1️⃣ The Problem: `guess.c`

### What the program is supposed to do
A simple guessing game: the user guesses a number, and the program says "Wrong guess!" or moves on.

### How to run it
```
./guess
```

### The bug observed
When the user guesses a number (e.g., `2`), instead of printing "Wrong guess!" once, the terminal gets flooded with repeated "Wrong guess!" messages — possibly infinitely.

```
$ ./guess
Guess: 2
Wrong guess!
Wrong guess!
Wrong guess!
Wrong guess!
... (continues forever)
```

Something is looping that shouldn't be.

---

## 2️⃣ The Tool: `debug50`

`debug50` is CS50's visual debugger, built into the VS Code IDE. It lets you:
- **Pause** your program at a chosen line using a **breakpoint**
- **Step Over** — execute one line at a time, watching variables change
- **Inspect** the current value of every variable in a panel on the left

### Key concepts
| Concept | What it means |
|---------|--------------|
| **Breakpoint** | A line you mark as a pause point — the program stops here when it reaches it |
| **Step Over** | Execute the next line of code and pause again |
| **Variables panel** | A live display of all variables currently in scope and their values |
| **Garbage value** | An uninitialized variable contains random bits from memory (e.g., `32764`) — never rely on it |

### Setting a breakpoint
In your code editor, click the left margin next to a line number. A pale red circle appears; click it to make it bright red. That line is now a breakpoint.

### Starting debug50
```
debug50 guess
```
This runs your compiled program under the debugger. When it hits the breakpoint, it pauses.

---

## 3️⃣ Debugging Session: Step Over

### Step 1 — Observe the initial state

When debug50 first pauses at the breakpoint, the Variables panel shows:
```
number = 0       ← not yet assigned (will be set on next line)
guess  = 32764   ← garbage value — uninitialized memory
```

- **Clarification — garbage values:** Before a variable is assigned a value, it holds whatever random bits happened to be at that memory address. This is called a **garbage value**. It could be `0`, `32764`, or anything else. Never read an uninitialized variable expecting a meaningful value.

### Step 2 — Step through line by line

**Step Over: `number = 5`**
```
number = 5      ← updated
guess  = 32764
```

**Step Over: `guess = get_int("Guess: ")`**
→ The terminal prompts for input. Type `3`, hit Enter.
```
number = 5
guess  = 3      ← updated
```

**Analysis:** guess (`3`) ≠ number (`5`), so the guess is wrong. We expect exactly one "Wrong guess!" then the program ends.

### Step 3 — Keep stepping and observe the loop

| Step | Line | Observation |
|------|------|-------------|
| Next | condition check | `guess != number` → `3 != 5` → **true** |
| Next | `printf("Wrong guess!\n")` | Prints "Wrong guess!" — seems correct |
| Next | **Goes BACK up to line 10** | ⚠️ The program didn't end — it looped! |
| Next | condition check again | `3 != 5` → still true |
| Next | `printf("Wrong guess!\n")` | Prints again |
| ... | ... | Infinite loop |

The yellow execution highlight keeps returning to the top of the loop. The program is running a `while` loop — which keeps repeating as long as the condition is true. Since neither `guess` nor `number` changes inside the loop, the condition is always true, and the loop never exits.

### Step 4 — Identify the bug

The code is using a **`while` loop** where an **`if` statement** should be:

```c
// BUGGY CODE
while (guess != number)
{
    printf("Wrong guess!\n");
    // guess and number never change → infinite loop!
}
```

A `while` loop is designed to repeat. An `if` statement checks once. For a single "is the guess wrong?" check, `if` is correct.

---

## 4️⃣ Fix 1: `while` → `if`

**Before (buggy):**
```c
while (guess != number)
{
    printf("Wrong guess!\n");
}
```

**After (fixed):**
```c
if (guess != number)
{
    printf("Wrong guess!\n");
}
```

| Statement | Behavior |
|-----------|----------|
| `while` | Checks condition, executes body, **loops back**, checks again → repeats until false |
| `if` | Checks condition once, executes body if true, **continues** — does NOT loop |

Recompile and run:
```
make guess
./guess
```

---

## 5️⃣ New Bug: Getting Both Messages

After the fix, typing `3` (when the answer is `5`) now prints:
```
Wrong guess!
You're correct!
```

Both messages appear for a wrong guess — that's still wrong. Debug50 is needed again.

### What's likely happening

Look at the structure of the conditions. There are two separate `if` statements instead of an `if` / `else`:

```c
// BUGGY
if (guess != number)
{
    printf("Wrong guess!\n");
}
if (guess == number)        // ← separate if, not else if
{
    printf("You're correct!\n");
}
```

With input `3` and number `5`:
- First `if`: `3 != 5` → **true** → prints "Wrong guess!" ✅
- Second `if`: `3 == 5` → **false** → should not print... unless the condition is wrong

**A likely alternate cause** — the "correct" print is outside any condition entirely:
```c
if (guess != number)
{
    printf("Wrong guess!\n");
}
printf("You're correct!\n");   // BUG: always runs, regardless of guess
```

### The fix — use `if` / `else`

```c
if (guess != number)
{
    printf("Wrong guess!\n");
}
else
{
    printf("You're correct!\n");
}
```

`if` / `else` guarantees exactly one branch runs — never both.

### Your turn
Carter leaves this second bug as an exercise: **use debug50, set a breakpoint, step through, and identify exactly why both messages appear in your version of guess.c.** The technique is the same as above — watch variable values and trace the execution path line by line.

---

## 6️⃣ The Step Over Debugging Workflow

| Step | Action |
|------|--------|
| 1 | Observe the unexpected output — describe exactly what's wrong |
| 2 | Open your code: `code guess.c` |
| 3 | Set a breakpoint at the start of the suspicious section |
| 4 | Run `debug50 guess` |
| 5 | Use **Step Over** to execute one line at a time |
| 6 | Watch the Variables panel for values that are wrong or unexpected |
| 7 | Identify the exact line where behavior diverges from your expectation |
| 8 | Fix the code |
| 9 | `make guess` → `./guess` to verify the fix |

---

## 7️⃣ Summary

### Key debugging concepts
| Concept | Meaning |
|---------|---------|
| Breakpoint | A pause point — execution stops here |
| Step Over | Execute the next line; treat any function call as a single step |
| Variables panel | Live view of all current variable values |
| Garbage value | Uninitialized variable holds random memory — always initialize before reading |

### The bugs in `guess.c`
| Bug | Cause | Fix |
|-----|-------|-----|
| Infinite "Wrong guess!" | `while` loop instead of `if` | Change `while` → `if` |
| Both messages print | Two separate `if` statements, or print outside conditions | Use `if` / `else` |

### `while` vs. `if`
| Keyword | Repeats? | Use when |
|---------|----------|----------|
| `while` | ✅ Yes — loops until condition is false | Repeating an action |
| `if` | ❌ No — checks once | Making a single decision |
