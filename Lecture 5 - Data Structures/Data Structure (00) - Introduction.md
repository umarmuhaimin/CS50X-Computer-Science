🗂️ Data Structures — Introduction (Lecture 5 @ CS50)

This is the final week of C in CS50. Everything you have learned — variables, arrays, pointers, memory allocation, structs — has been building toward this: **data structures**, which are simply different ways to organise data in memory. The choices you make here have real consequences on how fast your programs run and how much memory they use.

---

## 1. 🧠 What Are Data Structures?

A **data structure** is a way of organising data in memory so that it can be used efficiently. Think of it as choosing between different kinds of containers: a shelf, a stack of trays, a filing cabinet, a phone book — each one has different strengths and weaknesses depending on what you need to do with the data inside.

> **Key insight:** There is no single "best" data structure. Every choice is a **trade-off** between speed, memory, and complexity. Your job as a programmer is to understand the trade-offs and pick the right one for the problem.

---

## 2. 📌 Abstract Data Types vs. Concrete Data Structures

Before diving in, it helps to understand the difference between two related ideas:

| Term | What it means | Example |
|------|--------------|---------|
| **Abstract Data Type (ADT)** | A *concept* — what the structure does and what operations it supports, without specifying *how* it's built | "A queue that is FIFO" |
| **Concrete Data Structure** | An actual *implementation* in code — the real nuts and bolts of how the data is laid out in memory | An array or linked list used to build that queue |

Think of an ADT like a job description ("I need someone who can answer phones and file documents") and a concrete data structure like the actual person you hire to do that job. The same job can be done by many different people, just as the same ADT can be implemented with many different data structures.

### Common abstract data types we'll encounter

| ADT | Meaning | Real-world analogy |
|-----|---------|-------------------|
| **Queue** | FIFO — first in, first out | A line at an amusement park |
| **Stack** | LIFO — last in, first out | A stack of lunch trays |
| **Dictionary** | Key → Value mapping | A phone book (name → number) |

---

## 3. 🗺️ Overview of This Week's Data Structures

This week introduces **six concrete data structures**, each with its own strengths:

| # | Data Structure | Key Idea | Search Speed | Memory Use |
|---|---------------|----------|-------------|------------|
| 1 | **Arrays** | Contiguous memory blocks | O(log n) with binary search | Low |
| 2 | **Linked Lists** | Nodes stitched together with pointers | O(n) — no binary search | Medium (extra pointer per node) |
| 3 | **Trees** (Binary Search Trees) | Linked nodes in 2D — left/right children | O(log n) | Higher (2 pointers per node) |
| 4 | **Hash Tables** | Array + linked lists, using a hash function | O(1) best case, O(n) worst | Medium-high |
| 5 | **Tries** | Trees made of arrays, one node per letter | O(1) always | Very high |

We also revisit **queues and stacks** as abstract data types, and show how arrays and linked lists can implement them.

---

## 4. 🔑 New Syntax Introduced This Week

You already know `struct`, `.` (dot notation), and `*` (pointer/dereference). This week adds one more operator:

### The `->` (arrow) operator

The `->` operator is a shorthand that combines dereferencing a pointer and accessing a field inside a struct.

```c
// Without ->:
(*n).number = 1;   // dereference n, then access .number

// With ->: (much cleaner!)
n->number = 1;     // "go to the node n points to, then get its number"
```

`->` literally looks like an arrow, which matches the arrows we draw in memory diagrams. When you see `n->number`, read it as: **"follow the arrow from n, then get the number field."**

---

## 5. ⚖️ The Central Theme: Trade-Offs

Every data structure this week involves trade-offs. Professor Malan frames it this way: **"to gain something, you have to give something."**

| Want | Cost |
|------|------|
| Dynamic growth (no fixed size) | More memory (pointers) + slower copying |
| Faster search (O(log n) or O(1)) | More memory (extra pointers, bigger arrays) |
| Constant-time lookup O(1) | Potentially enormous memory usage |

As you progress through this week, notice how each new data structure is essentially an attempt to solve the limitations of the previous one — always trading one resource for another.

---

## 6. 📌 Summary

| Concept | Detail |
|---------|--------|
| **Data structure** | A way of organising data in memory |
| **ADT** | The concept (what operations it supports) |
| **Concrete structure** | The actual implementation |
| **New operator** | `->` — go to address, then access field inside struct |
| **Central theme** | Every choice is a trade-off: speed ↔ memory ↔ complexity |
| **This week covers** | Queues, Stacks, Arrays, Linked Lists, Trees, Hash Tables, Tries |
| **Context** | Last week of C — next week transitions to Python |
