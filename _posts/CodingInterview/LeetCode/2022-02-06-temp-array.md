---

title : Array Rotation Program

# layout: "post"

excerpt: "Array Rotation problem in geeksforgeeks"

categories:
  - CodingInterview
tags:
  - [Algorithm,CodingTest,CodingInterview]
# classes : wide
toc: true
toc_sticky: true
---
Problem Link : [Rotate Array | Practice | GeeksforGeeks](https://practice.geeksforgeeks.org/problems/rotate-array-by-n-elements-1587115621/1#)
I have a problem with a rotation array. This problem is to move the elements of the array to the left by d spaces. Elements outside offset 0 are positioned at the last offset. We will try to solve these problems by gradually improving the solution.

> **Your Task:**
> Complete the function **rotateArr**() which takes the array, D and N as input parameters and rotates the array by D elements. The array must be modified in-place without using extra space.
>
> **Expected Time Complexity:** O(N)
> **Expected Auxiliary Space:** O(1)
>
> **Constraints:**
> 1 <= N <= `10**6`
> 1 <= D <= `10**6`
> 0 <= arr[i] <= `10**5`





## temp array

```python
class Solution:
    #Function to rotate an array by d elements in counter-clockwise direction.
    def rotateArr(self,A,D,N):
        #Your code here
        D = D%N
        temp = A[:D]
        
        for i in range(D,N) :
            A[i-D] = A[i]
        A[N-D:] = temp
```

1. First of all, the value of D can be greater than the value of N. If you rotate N squares counterclockwise, the position remains the same, so assuming that D = m * N +r, moving N squares m times does not affect the element's position. So, let's find r squares using modular operation.
2. As the array is rotated counterclockwise, the elements in column D on the left are saved. Because, if you save the left, there is no need to save the state in the middle, but if you save the right part, you have to save the middle state, which increases the number of operations performed, so the left part is saved.
3. Move the right part by D spaces. Since each operation is a repetitive pattern, use a for statement to move it.
4. Copy the saved left part to the right part.

> **Time complexity :** O(n)
> **Auxiliary Space :** O(d)



## rotation each

As above, linear time complexity can be guaranteed with Big-o notation, but Auxiliary Space occurs. If Auxiliary Space is not allowed, you will have to seek another way.

```
class Solution:
    #Function to rotate an array by d elements in counter-clockwise direction.
    def rotateArr(self,A,D,N):
        #Your code here
        D = D%N
   
        for _ in range(D) :
            temp = A[0]
            for j in range(1,N) :
                A[j-1] = A[j]
            A[-1] = temp
        
```

1. First of all, the value of D can be greater than the value of N. If you rotate N squares counterclockwise, the position remains the same, so assuming that D = m * N +r, moving N squares m times does not affect the element's position. So, let's find r squares using modular operation.
2. First, save the element.
3. Move the remaining elements to the left by 1 space. Because it is a repetitive pattern, a for statement is used. It substitutes the first element stored in the last position.
4. Since the above operation 3 is repeated, a for statement is used.

> **Time complexity :** O(n * d)
> **Auxiliary Space :** O(1)



## juggling

In the above solution, Auxiliary Space is constant time, but Time Complexity is increased to O(n*d). If the constraint in your problem is that Time Complexity is O(n) and Auxiliary Space is O(1), then you should seek another way. The idea that Auxiliary Space is constant time was addressed in the above problem. It stores the number of elements that are not proportional to the size of the array. For Time Complexity to be linear time, as shown above, the element needs to be moved only once. How can we combine the above two ideas?

```python
class Solution:
    #Function to rotate an array by d elements in counter-clockwise direction.
    def gcd(self,a,b) :
        if b == 0 :
            return a
        else :
            return self.gcd(b , a%b)
    def rotateArr(self,A,D,N):
        #Your code here
        D = D%N
   
        g = self.gcd(N,D)
        for i in range(g) :
            temp = A[i]
            for j in range(1,N//g) :
                A[(i+(j-1)*D)%N] = A[(i+j*D)%N]
            else :
                A[(i-D)%N] = temp
                
```

The idea of storing the first element and moving the previous element by D spaces can be considered as an idea combining the two solutions above. However, there are caveats when applying this idea.

If gcd of n, d exists, the relation $$ n = m * g \quad d = q * g$$ is satisfied. m and q are mutually exclusive. If it is out of the range of offset when moving to the left by d spaces, N is added because it is a rotation relation. This can be obtained as a modular operation because the process is identical to the modular operation. Similarly, the offset(index) of the value entering the current position is the value added to the right from the current position. If the offset is out of range, N is subtracted, so it matches the result of the modular operation.

When moving d spaces to the left each time, the starting point is assumed to be the position of offset 0, and if moving d spaces to the left, the next offset will be $$ -D\ , \bmod\ , N $$ . The second move would be $$ ({(-D\, \bmod\, N)} - D ) \bmod N $$ . Since the result is the same even if the mod operation is overlapped, it is $$(-D\, \bmod\, N) - (D \bmod N)$$, so it is $$ ({(-2D\ , \bmod\, N)} - D ) becomes \bmod N $$ . If we apply mathmatical induction, assuming that $$(- kD\, \bmod\, N) $$ holds at k, similarly at k+1

$$
\begin{align}

(-kD\, \bmod\, N) - (D \bmod N) &= (-k-D\, \bmod\, N) \\

&= (-(k+1)D\, \bmod\, N)

\end{align}
$$

Since , we can derive the general expression. Since D is q * g, repeating this m times will bring it back to its original position. That is, you can see that the operation of incrementing i by 1 at the initial position and moving m times must be repeated d times.

1. First of all, the value of D can be greater than the value of N. If you rotate N squares counterclockwise, the position remains the same, so assuming that D = m * N +r, moving N squares m times does not affect the element's position. So, let's find r squares using modular operation.
2. Since there may be cycles, obtain gcd.
3. Since the pattern of moving m times is repetitive, use a for statement. Since it is a problem of moving from right to left, apply the solution solved above to move the value on the right to the left. Don't forget to save the initial value.
4. Since the pattern moving m times is repeatedly executed d times, use the for statement. Even if you change the starting index, the operation works as intended even if you apply the same pattern that moves m times because it is symmetrical.
5. For the last movement in m movement, assign the first value to the rotated offset.

## reverse

When the sequence of a is reversed, it will be called ra. If we divide a sequence into two parts a and b, let S = ab.

In this case, rS = rbra. If the order is reversed, the last offset becomes the starting offset. The starting offset of b is positioned as far as the length of b. Since the relationship between elements does not change, only the element of b is contained at a distance of length b from the start offset, so rb becomes the first part. Similarly, ra can also be applied.

So, if we reverse the sequence rarb, we get the sequence ba.

```
class Solution:
    #Function to rotate an array by d elements in counter-clockwise direction.
    def reverse(self,A,s,e) :
        for i in range(0 , (e-s+1)//2) :
            temp = A[s+i]
            A[s+i] = A[e-i]
            A[e-i] = temp

        
    def rotateArr(self,A,D,N):
        #Your code here
        D = D%N
   
        self.reverse(A,0,D-1)
        # print(A)
        self.reverse(A,D,N-1)
        # print(A)
        A = self.reverse(A,0,N-1)
        # print(A)
```

1. The reverse function swaps elements while gradually changing only half the length of the section by using that the sum of the start and end offsets of the section is constant even if each offset changes toward the center. Because it is a repetitive pattern, a for statement is used.
2. Reverse after slicing A based on column D.
3. Reverse the rest of the parts.
4. Reverse the whole.