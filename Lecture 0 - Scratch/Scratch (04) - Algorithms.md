🧮 Algorithms

1) Overview
 - Problem-solving is central to computer science and computer programming. An algorithm is a step-by-step set of instructions to solve a problem.

2) Phone Book Search Thought Experiment
 - Imagine the basic problem of trying to locate a single name in a phone book.
 - How might one go about this?
   - Approach 1: simply read from page one to the next to the next until reaching the last page.
   - Approach 2: search two pages at a time.
   - Approach 3: go to the middle of the phone book and ask, “Is the name I am looking for to the left or to the right?” Then, repeat this process, cutting the problem in half and half and half.
 - Each of these approaches could be called algorithms.

3) Big-O Picture (time vs size)
```
time to solve ↑
             |               n
             |              /
             |             /
             |            /
             |           /
             |          /   n/2
             |         /
             |        /
             |       /
             |   __--_______ log₂ n
             |__/____________________________→ size of problem
```

4) Interpreting the Curves
 - Notice that the first algorithm, highlighted in red, has a big-O of n because if there are 100 names in the phone book, it could take up to 100 tries to find the correct name.
 - The second algorithm, where two pages were searched at a time, has a big-O of n/2 because we searched twice as fast through the pages.
 - The final algorithm has a big-O of log2n, as doubling the problem would only result in one more step to solve the problem.

5) Takeaway
 - Programmers translate text-based, human instructions into code to solve problems.
