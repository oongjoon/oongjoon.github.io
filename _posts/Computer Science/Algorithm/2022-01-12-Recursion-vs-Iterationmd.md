---

title : Recursion vs Iteration

# layout: "post"

excerpt: "Recursion , Iteratoin"

categories:
  - Algorithm
tags:
  - [Algorithm,Recursion,Iteration,Computer Science]

toc: true
toc_sticky: true
---


I am studying data structures and algorithms, and the problem of using Recursion often comes up. Recursion's solution itself is not easy, but it seems more difficult to change it to iterative. There may be situations where you have to use Iterative because of the stack frame creation problem of Recursion itself. When converting in such a situation, there is no standard for how to do the conversion, so I thought that I had to memorize the solution every time. This recognizes that Recursion simply calls f(n-1) when returning, and Iterative is recognized as using for, while, so I felt that I was just solving the problem without a standard. In order to get a clear standard for this, I approached Recursion and Iterative theoretically with the mindset of an undergraduate student.

This content was written after listening to the MIT 6.001 recursion lecture.
## what is Recursion Algorithm

Professor Eric Grimson defines Recursion from two perspectives. This was taken exactly as the professor explained it.
- Algorithm
  - a way to solve problems by ***<u>divide-and-conquer</u>*** or ***<u>decrease-and-conquer</u>***
- Semantically
  - a programming technique where a ***<u>function call itself</u>***
    - in programming ,goal is NOT to have infinite Recursion
      - must have ***<u>1 or more base cases</u>*** that are easy to solve
      - must solve same problem on ***<u>some other input</u>*** with goal of simplifying the large input

Most of the time, I don't bring the description as it is, but I brought it because you defined it so well. I thought that this part should be unconditionally shared. I think it's the best explanation of Recursion I've heard so far. From an Algorithm perspective, we describe divide and decrease because the input changes to reach the base case. He emphasizes the ‘1 or more base cases’ part. Recursion will continue to call itself. This process must come to an end at some point. Therefore, as the base case, it sets where to end. This can be seen as a way to stop the program. The emphasis is on `some other input`, which means that no matter what input is given, it solves the same problem. Solves the same problem when input comes in, and ends when `base case` is reached. This is Recursion.


## What is Iterative Algorithm

This part has been taken from the description.

- looping constructs(while and for loops) lead to iterative algorithms
- can capture computation in a set of state variables that update on each iteration through loop

The iterative algorithm gets the state variable. And, while updating the state variable, the value in the iteration is computed and updated at every iteration. When the state variable satisfies a certain condition, it terminates. For example, let's listen.
### MULTIPLICATION - ITERATIVE SOLUTION

a * b is equivalent to adding a to itself b times.

- capture state by
  - an iteratoin number(i) starts at b
    - i  <- i-1  and stop when 0 
  - a current value of computation(result)
    - result <- result + a 

```
def mul(a,b) :
	result = 0
	while b > 0:
		b -= 1
		result += a
	return result
```



### Multiplication - recursive solution
Let's see how to think of a * b recusratively. It would be good to think about how to reduce the problem to a simpler and smaller problem.
$$
\begin{align}
a *b &= a+ a+ a .... a \\
&=  a + ( a+ a+ ..a) \\
& = a + a*(b-1)
\end{align}
$$


We reduced the problem a *b to a smaller problem a *(b-1) . By repeating this process over and over again, we arrive at the base case.

```
def mult(a,b) :
	if b== 1:
		return a
	else :
		return a + mult(a,b-1)
```

 

## Recursive in memory view

![image](https://user-images.githubusercontent.com/50165842/149150164-eb63ac0b-91d9-4e20-afbc-fbe567208a81.png)

Let's look at Recursive Program from the point of view of computer memory. Recursive Programming is a function that calls itself. When a function calls itself, the following process occurs.
1.  create new frame, scope
2. bind variables in scope , these variables are not change by recursive call
3. when reach base case, flow of control passes back to previous scope



## Recursion vs Iteration

![image](https://user-images.githubusercontent.com/50165842/149150508-005f6b5f-4d4c-4a7c-bde0-82df184a49c2.png)

Then, let's compare Recursion and Iteration.

- Recursion is more intuitive than Iteration.
- Recursion is efficient from the programmer's point of view.
- Recursion is ***<u>inefficient from a computer's point of view.</u>***

Why isn't Recursion so efficient from a computer point of view? Recursion isn't inefficient on all machines. It depends on the machine.

The example computer creates a new Frame every time the function is called. Therefore, it can be viewed as inefficient from a computer point of view. However, from a programmer's point of view, it can be said that it is efficient because it is easy to understand.
## Recursion Proof

I know Recursive is nice, but why does it work?



### inductive proof

```
def mul(a,b) :
	result = 0
	while b > 0:
		b -= 1
		result += a
	return result
```

Is iterative mutliplication terminated? Exit. Because b is the initial value, b is decreasing by 1 for every iteration inside the loop, and b is set to be greater than 0 as the execution condition of the loop. So, we know that someday it will be less than 1, and it will exit.


```
def mult(a,b) :
	if b== 1:
		return a
	else :
		return a + mult(a,b-1)
```

 Is recursive multiplication terminated?

- b = 1: The called mult(a,b) will end because there are no more recursive calls.
- b>1 : The called mult(a,b) will make a recursive call of mult for a smaller value of b. As b gradually decreases, we eventually reach the moment of b = 1 recursive call.

### mathmatical induction proof

This time we will use a mathematical tool. Mathematics is a good tool that gives you a way to interpret programs. Here, what we're going to use is mathematical induction. If we want to prove a claim, we'll make it referenceable by an integer (f(n)) .

To prove this for all natural numbers, we usually have to prove that it holds for n= 0 , n=1 . Let's prove the program used above, mult(a,b) by mathematical induction.

1. b = 1 : mult(a,b) = a * 1 = a
2. Suppose b = k : mult(a,k) returns the correct value.
3. b= k+1 : mult(a,k+1) = a + mult(a,k) , which returns the exact value.



It would be good to try other examples as well.


![러닝스텝 하노이탑 9단 : 다나와 가격비교](http://img.danawa.com/prod_img/500000/975/998/img/1998975_1.jpg?shrink=330:330&_v=20210520134149)


A famous example is the Tower of Hanoi. It is known that this example can be solved recursively.

```
def printMove(fr, to):
 	print('move from ' + str(fr) + ' to ' + str(to))
def Towers(n, fr, to, spare):
 if n == 1:
	printMove(fr, to)
 else:
    Towers(n-1, fr, spare, to)
    Towers(1, fr, to, spare)
    Towers(n-1, spare, to, fr)

```

Let us prove this by mathematical induction.

1. n = 1, the tower can be moved.
2. Assume that n = k-1 , Towers(k,fr,to,spare) yields the correct answer.
3. n =k , Towers(k,fr,spare,to) = Towers(k-1,fr,spare,to) ,Towers(1,fr,to,spare) , Towers(k-1 , fr,spare , to) , and k also yield correct answers.