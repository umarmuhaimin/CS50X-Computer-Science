😺 Scratch Programming

1) Hello World
 - To begin, drag the “when green flag clicked” building block to the programming area. Then, drag the say building block to the programming area and attach it to the previous block.

```
when green flag clicked
say hello, world
```

 - Notice that when you click the green flag now on the stage, the cat says, “hello, world.”
 - Notice that the input hello, world is passed to the function say, and the side effect of that function running is the cat saying hello, world.

2) Hello, You
 - We can make your program more interactive by having the cat say hello to someone specific. Modify your program as below:

```
when green flag clicked
ask What's your name? and wait
say join hello, answer
```

 - Notice that when the green flag is clicked, the function ask is run. The program prompts you, the user, What's your name? It then stores that name in the variable called answer. The program then passes answer to a special function called join, which combines two strings of text hello, and whatever name was provided. The value of answer is passed as an argument to join. These collectively are passed to the say function. The cat says, Hello, and a name. Your program is now interactive.
 - Notice that the inputs hello, and answer are provided to join, which returns hello, David. This return value is then passed to say, which produces the side effect of the cat speaking.

3) Speak Instead of Say
 - Quite similarly, we can modify our program as follows:

```
when green flag clicked
ask What's your name? and wait
speak join hello, answer
```

 - Notice that this program, when the green flag is clicked, passes the same variable, joined with hello, to a function called speak.

4) Meow, Loops, and Abstraction
 - Along with pseudocoding, abstraction is an essential skill and concept within computer programming.
 - Abstraction is the act of simplifying a problem into smaller and smaller problems.
 - For example, if you were hosting a huge dinner for your friends, the problem of having to cook the entire meal could be quite overwhelming! However, if you break down the task of cooking the meal into smaller and smaller tasks (or problems), the big task of creating this delicious meal might feel less challenging.

   First version (repetition):

```
when green flag clicked
play sound Meow until done
wait 1 seconds
play sound Meow until done
wait 1 seconds
play sound Meow until done
```

 - Notice that you are doing the same thing over and over again. Indeed, if you see yourself repeatedly coding the same statements, it’s likely the case that you could program more artfully – abstracting away this repetitive code.

   Looping version:

```
when green flag clicked
repeat 3
  play sound Meow until done
  wait 1 seconds
```

 - Notice that the loop does exactly as the previous program did. However, the problem is simplified by abstracting away the repetition to a block that repeats the code for us.

   Custom block version:

```
define meow
  play sound Meow until done
  wait 1 seconds

when green flag clicked
repeat 3
  meow
```

 - Notice that we are defining our own block called meow. The function plays the sound meow, and then waits one second. Below that, you can see that when the green flag is clicked, our meow function is repeated three times.

   Parameterized custom block:

```
define meow n times
repeat n
  play sound meow until done
  wait 1 seconds
```

 - Notice how n is taken from “meow n times.” n is passed to the meow function through the define block.
 - Overall, notice how this process of refinement led to better and better-designed code. Further, notice how we created our own algorithm to solve a problem. You will be exercising both of these skills throughout this course.

5) Conditionals
 - Conditionals are an essential building block of programming, where the program looks to see if a specific condition has been met. If a condition is met, the program does something.
 - To illustrate a conditional, write code as follows:

```
when green flag clicked
forever
  if touching mouse-pointer ? then
    play sound Meow until done
```

 - Notice that the forever block is utilized such that the if block is triggered over and over again, such that it can check continuously if the cat is touching the mouse pointer.

   Video sensing variant:

```
when video motion > 10
  play sound Meow until done
```

 - Remember, programming is often a process of trial and error. If you get frustrated, take time to talk yourself through the problem at hand. What is the specific problem that you are working on right now? What is working? What is not working?



