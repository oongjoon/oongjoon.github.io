---
title : "General form and definition of Python loop statement. How can you not use the Flag variable in a loop?"



excerpt: "for and while detail in Python"

categories:
  - Python
tags:
  - [Python,loop,Statemens,LearningPython,Chp13Loop]
# classes : wide
toc: true
toc_sticky: true
---
# Python loop statements general format

There are two loop statements available in Python. One is for statement and the other is while statement. Everyone knows roughly how to use it.

However, you will not be familiar with the semantic definition and general syntax for this. So, while studying is bad for learning a programming language, I believe that formulating is a good way to apply this logic to a problem you're not seeing for the first time. For statements allowed only in for statment, while statement, and loop, we will show it with semantic definition and general syntax.





# while

> while statement represents a general loop.

That is, it is a statement to make a general proecedure. You can make all kinds of loops with while loop.

The general format of while is as follows.

```
while test : # test expression
statements # intended expression
if test : continue # Go to top of loop
if test : break #Exit loop
[else:] # Optional
statements
```





# for

> The for loop is a statement that searches items in iterable objects or sequences.

Iterable objects can include lists, tuples, sets, user-defiend iterable objects, etc.

The general format of the for statement is as follows.

```
for target in iterable : # Assign item in iterable to target
statements
if test : continue # Go to top of loop
if test : break #Exit loop
[else:] # Optional
statements

```

## target assignment

`target in iterable` can be viewed as an assignment statement. That is, the item in the iterable is assigned to the target.

As an assignment, there is no limit to any kind of assignent.



```
for (a,b) in S :
print(a, b)
```

Tuple type assignment is also possible.

```
for target in S :
a,b = target
```

You can also unpack the sequence like this.

In addition, as any type of expression is possible in the target, nested expression is also possible.

```
for ((a,b),c) in S :
print(a, b, c)

```

In addition, as any type of expression is possible in the target, nested expression is also possible.





```
for (a, *b, c) in S :
print(a, b, c)
```

A starred expression is also possible.



***<u>In other words, any syntax allowed in Python assignment can be used.</u>***

# Continue, break, loop else

## continue , break

continue and break are often used, but let's define them.

The continue statement is a statement that immediately goes straight to the top of the loop.

The break statement is a statement that immediately exits the loop.

## loop else

loop else is executed when the test of the loop statement becomes False. Alternatively, it can be used when the iterable is exhausted in the for loop. However, if the loop ends with a break statement, it is not executed.

This is similar to the try except statement, where else in the try statament is executed if except is not executed, and in for loop or while loop, else is executed if no break is executed.

When executing a loop using flag status in loop, if the desired condition is not satisfied and the entire loop is iterated, the coding structure using flags is often used. However, it can be seen that using loop else provides a more explicit coding structure.

In other words, it allows you to control the flow out of the loop flow without setting flag status or other conditions.

```
for n in range(2, 10):
... for x in range(2, n):
    ... if n % x == 0:
        ... print(n, 'equals', x, '*', n//x)
        ... break
... else:
... # loop fell through without finding a factor
... print(n, 'is a prime number')
...
2 is a prime number
3 is a prime number
4 equals 2 * 2
5 is a prime number
6 equals 2 * 3
7 is a prime number
8 equals 2 * 4
9 equals 3 * 3
```

This is an example of finding a prime number, but if the factor is not found, it uses a loop else to tell it is a prime number.

```
for n in range(2, 10):
flag = False
... for x in range(2, n):
... if n % x == 0:
... print(n, 'equals', x, '*', n//x)
flag = True
... break
...if not flags:
print(n, 'is a prime number')
...
2 is a prime number
3 is a prime number
4 equals 2 * 2
5 is a prime number
6 equals 2 * 3
7 is a prime number
8 equals 2 * 4
9 equals 3 * 3
```

If there is no loop else, it will use a non-natural coding structure using flag status.

```
while x: # Exit when x empty
     if match(x[0]):
         print('Ni')
         break # Exit, go around else
     x = x[1:]
else:
     print('Not found') # Only here if exhausted x
```

If it cannot be found through the while loop, the loop else will handle the flow.

```
found = False
while x and not found:
     if match(x[0]): # Value at front?
         print('Ni')
         found = True
     else:
         x = x[1:] # Slice off front and repeat
if not found:
     print('not found')
```

If you do not use loop else, you will have to handle the flow that is not found outside the loop with flag status.
