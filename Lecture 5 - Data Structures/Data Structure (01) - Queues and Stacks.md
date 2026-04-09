📦 Queues and Stacks (Lecture 5 — Data Structures @ CS50)

Queues and stacks are the first **abstract data types** we study. They are not so much about *how* data is stored in memory, but about *what rules* govern how data enters and leaves the structure. These two structures are everywhere in real life — and everywhere in computing.

---

## 1. 🧠 Abstract Data Types — A Quick Recap

An **abstract data type** defines:
- What the structure *does* (its properties and operations)
- **Not** how it is built internally

This matters because the same abstract idea can be built using arrays, linked lists, or other structures. The choice of implementation affects speed and memory, but the external behaviour stays the same.

---

## 2. 🚶 Queues — First In, First Out (FIFO)

### The idea

A **queue** works exactly like a queue at an amusement park: whoever gets in line first gets served first. The person who just arrived goes to the back.

```
Front of queue (served first)          Back of queue (most recent)
         [Person 1] [Person 2] [Person 3] [Person 4]
              ↑ gets out first              ↑ joined last
```

- **Clarification — FIFO:** Stands for **First In, First Out**. The first element to enter the queue is the first element to leave it.

### Operations

| Operation | What it does |
|-----------|-------------|
| **Enqueue** | Add an item to the back of the queue (join the line) |
| **Dequeue** | Remove an item from the front of the queue (leave the line) |

### In code (C)

```c
const int CAPACITY = 50;

typedef struct
{
    person people[CAPACITY];
    int size;
}
queue;
```

- `people[CAPACITY]` — the array holding up to 50 people
- `size` — how many people are actually in the queue right now (as opposed to `CAPACITY`, which is the maximum)

### Real-world analogy

Your email inbox, viewed oldest-first. The emails you received earliest sit at the top and should ideally be dealt with first.

### The limitation ⚠️

This implementation uses a **fixed-size array**. The queue can hold at most 50 people. If a 51st person wants to join, the data structure has no room. You'd have to recompile the code with a bigger number — which is clearly not ideal for a real application.

> **This is why queues (and stacks) are called *abstract* data types:** the FIFO property defines what a queue *is*, but the underlying implementation can be improved. A dynamic structure like a linked list could make it grow as needed.

---

## 3. 🥞 Stacks — Last In, First Out (LIFO)

### The idea

A **stack** works like a stack of trays in a cafeteria. Trays are added to the top and removed from the top. The tray placed there most recently is the first one taken.

```
         TOP ← most recently added (taken first)
        [Tray 4]
        [Tray 3]
        [Tray 2]
        [Tray 1]  ← placed here first (taken last)
         BOTTOM
```

- **Clarification — LIFO:** Stands for **Last In, First Out**. The last element added to the stack is the first one removed.

### Operations

| Operation | What it does |
|-----------|-------------|
| **Push** | Place something on top of the stack |
| **Pop** | Remove something from the top of the stack |

### In code (C)

```c
const int CAPACITY = 50;

typedef struct
{
    person people[CAPACITY];
    int size;
}
stack;
```

Notice this looks almost identical to the queue struct — the difference is purely in *behaviour* (which end you add to and remove from), not in structure.

- **Clarification — why same struct?** Both a queue and a stack can be implemented as an array with a size counter. The difference is in the logic of your `enqueue`/`dequeue` vs `push`/`pop` functions: a stack always adds and removes from the same end (the top), while a queue adds to one end and removes from the other.

### Real-world analogies

| Analogy | Why it's a stack |
|---------|-----------------|
| Cafeteria trays | New trays go on top; everyone takes from the top |
| Clothes in a box (Jack's problem) | Last washed item goes back on top; always wear from the top |
| Email inbox (newest first) | Most recent email is at the top; most people open that one first |

### Jack's story — the hidden lesson

The story of Jack (who always took his clothes from the top of a box and put them back on top) illustrates a **stack in real life**. He wore the same few items repeatedly while the rest sat at the bottom — a consequence of LIFO behaviour. Lou's solution was to use a **queue** (hanging clothes in a closet, adding on the left, taking from the right) so every item gets used before anything is worn twice.

---

## 4. ⚖️ Queue vs. Stack Side by Side

| Property | Queue | Stack |
|----------|-------|-------|
| **Ordering** | FIFO — First In, First Out | LIFO — Last In, First Out |
| **Add to** | Back of the line | Top |
| **Remove from** | Front of the line | Top |
| **Add operation** | Enqueue | Push |
| **Remove operation** | Dequeue | Pop |
| **Real-world analogy** | Line at a store, fair email inbox | Cafeteria trays, browser back button |
| **Fairness** | ✅ Fair — whoever waited longest goes first | ❌ Can be unfair — recent items always take priority |

---

## 5. ⚠️ The Shared Limitation — Fixed Capacity

Both implementations above use a statically-sized array (`CAPACITY = 50`). This means:

1. **Under-allocating** — If more than 50 people want in, you're stuck. You have to recompile.
2. **Over-allocating** — If you set `CAPACITY = 5000` but only 3 people show up, you've wasted enormous memory.

The solution is to make these data structures **dynamic** — able to grow and shrink as needed. This is exactly what linked lists (next file) allow us to do.

---

## 6. 📌 Summary

| Concept | Detail |
|---------|--------|
| **Queue** | FIFO — first in, first out |
| **Enqueue** | Add to the back |
| **Dequeue** | Remove from the front |
| **Stack** | LIFO — last in, first out |
| **Push** | Add to the top |
| **Pop** | Remove from the top |
| **Both implemented as** | Array + size counter (in their simplest form) |
| **Shared limitation** | Fixed capacity — can't grow beyond the declared size |
| **Solution** | Use a dynamic structure (like a linked list) instead of a fixed array |
