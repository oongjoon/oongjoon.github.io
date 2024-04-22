---
title : "Vector,Matrix,Linear Transformation"



excerpt: "3Blue1Brown Linear Algebra :Lec 01~05"

categories:
  - Linear Algebra
tags:
  - [Mathmathics,Machine Learning,deep learning,Linear algebra ,3blue1brown]
# classes : wide
toc: true
toc_sticky: true
---

With the emergence of the DataCentric AI trend and the emergence of Large Models (ex.LLM) with over 100 billion parameters, many deep learning engineers can easily create a model that solves the desired problem if given an appropriate and sufficient amount of data. However, creating data and increasing parameters requires significant resources. Therefore, Engineers or Scientists still need Problem Solving skills to split an existing problem into multiple subproblems or change the details of a widely used popular model. If you look at the DeepLearning Model from a low-level perspective, it consists of Matrix Multiplication. In other words, you must know how to interpret Matrix and Vector operations. Rather than simply thinking of it as scalar multiplication, we need to know what the mathematical meaning is. In this article, we will start by looking at Vector and Matrix from a geometric perspective.


## Vector
DeepLearning consists of matrix multiplication and does not seem to have anything to do with vectors. However, the rows and columns of the matrix are vectors. Therefore, Matrix can be seen as a concatenation of Vectors. Therefore, in order to understand Matrix Multiplication, you must understand Vector.
### What is it?
What you think of Vector will depend on your major.
- Physics:  an arrow with length and direction.
- Computer Science:  List of Numbers. Each Number has some meaning.
- Math: object that can perform operations such as Add and Multiply.



### Operation and Coordinate Systems
A vector is an arrow located in a certain coordinate system. There is a starting point and an ending point, but unlike in Physics, from the perspective of Linear Algebra, a Vector is always the same vector no matter where it is located. In other words, if there is a vector on the xy plane, the starting point can always be located at the origin.
From a Computer Science perspective, Vector $$ \begin{bmatrix} 2 \\ 3 \end{bmatrix}$$ is +2 away from the y-axis and +3 away from the x-axis.
Addition of a vector connects the starting point of the A vector and the ending point of the B vector by placing the starting point of the B vector at the end of the A vector. If you see this in the Coordinate System, $$ \begin{bmatrix} x1 \\ y1 \end{bmatrix} + \begin{bmatrix} x2 \\ y2 \end{bmatrix}$$ is $$ \begin{bmatrix} x1+x2 It becomes \\ y1+y2 \end{bmatrix}$$.
Multiplication of a vector means multiplying the vector by a number. For example, $$ c \cdot \begin{bmatrix} 2 \\ 3 \end{bmatrix} = \begin{bmatrix} 2 \cdot c \\ 3\cdot c \end{bmatrix} $$. If you multiply the Vector by 1.7, the length of the Vector will be 1.7 times, and if you multiply the Vector by -3, the Vector will flip horizontally based on the straight line where the Vector is, and its length will be tripled. This is called “Scaling.”

  <!-- $$ c \cdot \begin{bmatrix} 2 \\ 3 \end{bmatrix} = \begin{bmatrix} 2 \cdot c \\ 3\cdot c \end{bmatrix} $$ -->

#### n-dim vector

Vectors can exist on the xyz plane and also on the n-dimension plane. For example, a vector named $$ \begin{bmatrix} 2 \\ 3 \\ 4\end{bmatrix}$$ may exist. A 6-dimension vector $$ \begin{bmatrix} 2 \\ 3 \\ 4 \\5 \\ 6 \\ 7 \end{bmatrix}$$ may exist. The operations of addition and multiplication are equally applied to these vectors.


  <!-- $$ \begin{bmatrix} 2 \\ 3 \\ 4\end{bmatrix}$$ -->
## Linear Combination
I looked at the operation of the vector, and the operation of the vector is calculated by multiplying the vector by a specific scalar. In actual vector operations, you cannot know which scalar will be multiplied, so you must know how to interpret the operation of a vector multiplied by an arbitrary scalar. This is called Linear Combination, and let’s find out how to interpret it from a geometrical perspective.
### basis vector
For example, if xy coordinate is given, we interpret it as x coordinate x1 and y coordinate y1. However, from now on, we will just consider this as a scalar.
The xy coordinate has special vectors $$\hat{i} and \hat{j}$$. $$\hat{i} = \begin{bmatrix} 1 \\ 0 \end{bmatrix} , \hat{j} = \begin{bmatrix} 0 \\ 1 \end{bmatrix} $$ .
For example, vector $$ \begin{bmatrix} 3 \\ 2 \end{bmatrix} $$ becomes $$ 3\cdot \hat{i} + 2 \cdot \hat{j}$$.

#### different basis vector

If the basis vector is $$\hat{i} = \begin{bmatrix} 2 \\ 1 \end{bmatrix} , \hat{j} = \begin{bmatrix} 1 \\ 2 \end{bmatrix} $$ I would say so. At this time, vector $$ \begin{bmatrix} 3 \\ 2 \end{bmatrix} $$ is $$ \begin{bmatrix} 3 \\ 2 \end{bmatrix} $$ is $$ 3\cdot \hat{i } + 2 \cdot \hat{j} =3 \cdot \begin{bmatrix} 2 \\ 1 \end{bmatrix} + 2 \cdot \begin{bmatrix} 1 \\ 2 \end{bmatrix} = \begin{ bmatrix} becomes 8 \\ 7 \end{bmatrix} $$.

### Span
The span of a vector is a set of vectors that can be obtained when a basis vector is multiplied by an arbitrary scalar. Let's look at several examples.

- case1 :$$ \overrightarrow a + y \cdot \overrightarrow b ,\qquad \overrightarrow a \neq \emptyset , \overrightarrow b \neq \emptyset $$
   - In this case, an arbitrary straight line will be the span.
- case2 :$$ neq 0 $$
   - In this case as well, a random straight line will become a span.
- case3 :$$
   - In this case, it's going to be the origin.
  
#### Vector as Points
When considering a vector, you will consider span, but if you consider a vector as a line, you must consider countless lines. Therefore, we will consider Vector as Point.

## Linear Transformation
Linear Combination can be seen as a multiplication of Vector and Matrix. Vector Operation was analyzed in Geometrical View, but Multiplication of Vecotr and Matrix could not be analyzed in Geometrical View. Let's analyze the multiplication of vector and matrix from a geometrical perspective.

### Why use term Transformation?
When viewing a Vector as a Point, converting the vector can be viewed as a function. Transformation can be seen as visualizing vectors and moving all input vectors to create an output vector.
### Non-linear vs. Linear
Arbitrary transformations are complicated to define, but in linear algebra, transformations are limited to special forms.
This is called Linear Transformation and satisfies the following properties.
- All lines are straight without bending even after transformation.
- The origin remains the origin even after transformation.
- The grid remains “parallel” and maintains the same “spacing” even after trnasformation.

So, what are some examples of non-linear transformation?
- Transformation that causes the grid line to bend
- Maintains a straight line, but moves the origin
- The grid line remains parallel, the origin is maintained, and the interval between the grid lines changes, thus transforming an arbitrary straight line into a curve.


### Vector view
Let’s look at linear transformation from a vector perspective. To see it from a vector perspective, you only need to know how the two basis vectors $$ \hat i, \hat j$$ are transformed. The transformation of other vectors is obtained as a transformed basis vector.
For example, let's say $$ \overrightarrow v = -1 \cdot \hat i + 2 \cdot \hat j $$.
$$ \overrightarrow v $$ after transformation is the addition of -1 times $$ \hat i$$ and 2 times $$\hat j $$ after transformation. Because, before transformation, $$ \overrightarrow v $$ consists of basis vectors $$ \hat i, \hat j $$, and maintains the same linear combination after transformation.
That is, $$ \overrightarrow transformed \ v = -1 \cdot \hat transformed \ i + 2 \cdot \hat transformed \ j $$. Therefore, if you know the transformed $$ \hat i, \hat j $$, you can infer the transformed $$ \overrightarrow v $$.

### Matrix View
You can look at this from a matrix perspective rather than a vector perspective.
For example, let's consider a matrix,vector called $$ \begin{bmatrix} a & b \\ c & d \end{bmatrix} \begin{bmatrix} x \\ y \end{bmatrix}$$. This is the equation $$ x \cdot \begin{bmatrix} a \\ b \end{bmatrix} + y \cdot \begin{bmatrix} c \\ d \end{bmatrix} $$. That is, $$ \begin{bmatrix} a \\ b \end{bmatrix} $$ becomes $$ \hat transformed \ i $$ , $$ \begin{bmatrix} c \\ d \end{bmatrix} $$ You can think of it as $$ \hat transformed \ j $$. x,y will be the scaler for the basis vector.
For example, for clockwise 90 degree rotation, $$ \begin{bmatrix} 0 & 1 \\ -1 & 0 \end{bmatrix} $$ would be, and $$ \hat transformed \ i = \begin{bmatrix} 0 \\ 1 \end{bmatrix} , \hat transformed \ j = \begin{bmatrix} -1 \\ 0 \end{bmatrix} $$ .



### Composition of Matrices
We learned how to view Linear Transformation from a Matrix perspective. So, what happens if Linear Transformation is applied continuously?
> It will be an arbitrary linear transformation.


This is expressed as matrix multiplication. Let's take an example.
$$\begin{bmatrix} 0 \ 1 \\ -1 \ 0 \end{bmatrix} $$, which is 90 degree clockwise rotation, and $$\begin{bmatrix} 0 \ 1 \\ -1 \ 0 \end, which is sheer transformation. Let’s apply {bmatrix} $$. The new $$ \hat i , \hat j $$ will be $$ \begin{bmatrix} 1 \\ 1 \end{bmatrix} , \begin{bmatrix} -1 \\ 1 \end{bmatrix}$$ respectively. It will. In other words, $$ \begin{bmatrix} 1 &-1 \\ 1& 0 \end{bmatrix} $$ becomes the final linear transformation. This can be viewed as one transformation rather than two consecutive transformations.
Therefore, $$ \begin{bmatrix} 1& 1 \\ 0& 1 \end{bmatrix} \begin{bmatrix} 0 &-1 \\ 1& 0 \end{bmatrix} = \begin{bmatrix} 1 &-1 \\ 1 Since &0 \end{bmatrix} $$, the product of the two matrices is equivalent to applying one transformation and applying another transformation from a geometric perspective.
Here, you need to remember to apply the transformation on the right first, then the transformation on the left. Since it is derived from the composite function f(g(x)), it must be read from right to left.

Any Matrix $$ \begin{bmatrix} 0 & 2 \\ -1 & 0 \end{bmatrix} \begin{bmatrix} 1 &1 \\ -2& 0 \end{bmatrix} = \begin{bmatrix} ?& ? \\ ?& ? \end{bmatrix} Let's consider $$. Here the new matrix will be the final $$ \hat i ,\hat j $$.
From the left, we will name the matrices M1 and M2, respectively. After M2 transformation, $$ \hat i, \hat j$$ becomes $$ \begin{bmatrix} 1 \\ 1 \end{bmatrix} , \begin{bmatrix} -2 \\ 0 \end{bmatrix} $$ It's possible. If M1 transformation is applied continuously, $$ \hat i $$ is $$\begin{bmatrix} 0 &2 \\ -1 &0 \end{bmatrix} \begin{bmatrix} 1 \\ 1 \end{bmatrix} \begin {bmatrix} 2 \\ 1\end{bmatrix} $$ , $$ \hat j $$ is $$\begin{bmatrix} 0 &2 \\ -1& 0 \end{bmatrix} \begin{bmatrix} -2 \ It will be \ 0 \end{bmatrix} \begin{bmatrix} 0 \\ -2\end{bmatrix} $$ .

### Matrix Multiplication Order is Important?
Does the order of Matrix Products matter? The conclusion is yes.
You can prove this mathematically, but it's also good to think about it from a geomteric perspective. From a geometric perspective, they will not always be the same because different transformations are applied to two different basis vectors.
### Matrix associativity
The fact that $$ (AB)C = A(BC) $$ holds is called the associativty rule. This can be proven by setting up an arbitrary matrix, but it can also be proven from a geometric perspective.
In the case of (AB)C, the transformation of C is applied and then the transformation of (AB) is applied. Since the product of the matrix represents continuous transformationi, a basis vector will be obtained by continuously applying the transformations of B and A.
In the case of A(BC), since (BC) also represents a continuous transformation, the transformation of A will be applied after applying the transformation of C and B. Since the same transformation is applied in the same order, the associativity rule is established.

## Summary

In this article, we looked at Vector, Matrix, and Linear Transformation from Geometrical View. A summary of today's content is as follows.
- The linear combination of any scalar represents a space of a specific dimension and is called a span.
- Linear Combination can be expressed as Vector-Matrix Multiplication, which transforms a basis vector.
- Performing Linear Transformation multiple times is the same as multiplying the corresponding Matrix.


## References
[3blue1brown-linear algebra](https://www.youtube.com/watch?v=XkY2DOUCWMU&list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab&index=4)