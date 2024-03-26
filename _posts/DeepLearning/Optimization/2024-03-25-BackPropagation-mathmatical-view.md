---
title : " BackPropagatoin Mathmatical Derivation   "



excerpt: "FCN backpropagation in Mathematical view"

categories:
  - Optimization
tags:
  - [Machine Learning,deep learning ,derivation]
# classes : wide
toc: true
toc_sticky: true
usemathjax : true
---
In the last [post](https://woongjoonchoi.github.io/dlarchitecture/Feed-Forward-Network/), we showed the process of deriving FeedForwrad Network's forward propagation using a formula. This time, we will show the process of deriving backward propagation using a formula. 

$$ \frac{\delta J }{\delta W_{(0,0)}^{[l]}}  ,  \frac{\delta J }{\delta b^{[l]}} $$
$$ \frac{\delta J }{\delta W_{(0,0)}^{[l]}}  = \frac{\delta J }{\delta Z_{(0,0)}^{[l]}}  \frac{\delta Z_{(0,0)}^{[l]}}{\delta W_{(0,0)}^{[l]}}  $$


$$ \sum_{i=0}^m$$

$$ \partial $$
$$ \frac{\partial{ \partial J}}{\partial w_{1, 1}^{[l]}}$$
## $$ \frac{\delta J }{\delta W^{[l]}}  ,  \frac{\delta J }{\delta b^{[l]}} $$ 


First, let's consider only $$ \frac{\delta J }{\delta W_{(0,0)}^{[l]}} $$.
$$
\begin{align}
  \frac{\delta J }{\delta W_{(0,0)}^{[l]}} &= \sum_{i=0}^m \frac{\delta J }{\delta Z_{(0 ,i)}^{[l]}} \frac{\delta Z_{(0,i)}^{[l]}}{\delta W_{(0,0)}^{[l]}} \ newline &= \sum_{i=0}^m \frac{\delta J }{\delta Z_{(0,i)}^{[l]}} a_{(0,i)}^{[l] }
  \end{align}
  $$
If we only consider (0,0) of the weight matrix, the above equation is established. If you change the row of W, the row of Z will be parameterized and changed. If you change the column of W, the row of A will change. Therefore, the following equation can be derived. 

$$
\begin{align}
 \frac{\delta J }{\delta W_{(j,k)}^{[l]}}  &= \sum_{i=0}^m \frac{\delta J }{\delta Z_{(j,i)}^{[l]}}  \frac{\delta Z_{(j,i)}^{[l]}}{\delta W_{(j,k)}^{[l]}}  \newline &=  \sum_{i=0}^m \frac{\delta J }{\delta Z_{(j,i)}^{[l]}}  a_{(k,i)}^{[l]}
 \end{align}
 $$   

In order to vectorize this into a matrix form, let us first consider only one element of W. To make it a matrix, it must be the product of the vector of (1,m) and the vector of (m,1). The derivative of Z is in the form of increasing columns, and A is also in the form of increasing columns. Since the matrix obtained from the cache is A and Z will be calculated, it is natural to transpose A.


$$ 
\begin{align*}
&
\begin{bmatrix}
\frac{\partial{ \partial J}}{\partial w_{1, 1}^{[l]}} & \dots & \frac{\partial{ \partial J}}{\partial w_{1, k}^{[l]}} & \dots & \frac{\partial{ \partial J}}{\partial w_{1, n^{[l-1]}}^{[l]}} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
\frac{\partial{ \partial J}}{\partial w_{j, 1}^{[l]}}& \dots & \frac{\partial{ \partial J}}{\partial w_{j, k}^{[l]}}& \dots & \frac{\partial{ \partial J}}{\partial w_{j, n^{[l-1]}}^{[l]}}\\
\vdots & \ddots & \vdots & \ddots & \vdots \\
\frac{\partial{ \partial J}}{\partial w_{ n^{[l]} , 1}^{[l]}}& \dots & \frac{\partial{ \partial J}}{\partial w_{ n^{[l]} , k}^{[l]}}& \dots & \frac{\partial{ \partial J}}{\partial w_{ n^{[l]} , n^{[l-1]}}^{[l]}}
\end{bmatrix} \\
&=
\begin{bmatrix}
\frac{\partial{ \partial J}}{\partial Z_{1, 1}^{[l]}} & \dots & \frac{\partial{ \partial J}}{\partial Z_{1, i}^{[l]}}  & \dots & \frac{\partial{ \partial J}}{\partial Z_{1, m}^{[l]}} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
\frac{\partial{ \partial J}}{\partial Z_{j, 1}^{[l]}} & \dots & \frac{\partial{ \partial J}}{\partial Z_{j, i}^{[l]}} & \dots & \frac{\partial{ \partial J}}{\partial Z_{j, m}^{[l]}} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
\frac{\partial{ \partial J}}{\partial Z_{n^{[l]}, 1}^{[l]}} & \dots & \frac{\partial{ \partial J}}{\partial Z_{n^{[l]}, i}^{[l]}}& \dots & \frac{\partial{ \partial J}}{\partial Z_{n^{[l]}, m}^{[l]}}
\end{bmatrix} \notag \\
&\ \cdot
\begin{bmatrix}
a_{1, 1}^{[l - 1]} & \dots & a_{k, 1}^{[l - 1]} & \dots & a_{n^{[l - 1]}, 1}^{[l - 1]} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
a_{1, i}^{[l - 1]} & \dots & a_{k, i}^{[l - 1]} & \dots & a_{n^{[l - 1]}, i}^{[l - 1]} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
a_{1, m}^{[l - 1]} & \dots & a_{k, m}^{[l - 1]} & \dots & a_{n^{[l - 1]}, m}^{[l - 1]}
\end{bmatrix},

\end{align*}
 

$$ 

When you finally expand the formula, it will unfold as shown above. If you vectorize this into matrix form, it will come out as follows.

$$\frac{\delta J }{\delta W^{[l]}} = \frac{\delta J }{\delta Z^{[l]}} \cdot {A^{[L-1] }}^T$$

This time, we will only think about $$ \frac{\delta J }{\delta b_{j}^{[l]}} $$.
$$ \frac{\delta J }{\delta b_{j}^{[l]}} = \sum_{i=0}^{m} \frac{\delta J }{\delta Z_{j,i }^{[l]}}$$
  If we vectorize all b and write it down, it will look like this:

$$ \frac{\delta J }{\delta b^{[l]}}  = \sum_{axis=1} \frac{\delta J }{\delta Z^{[l]}} $$ 


## $$ \frac{\delta J }{\delta Z^{[l]}} $$

This time, we will only think about $$ Z_{0,0}^{[l]}$$.

$$ \frac{\delta J }{\delta Z_{(0,0)}^{[l]}} = \sum_{p=0} \frac{\delta J }{\delta A_{(k, 0)}^{[l]}} \frac{\delta A_{(k,0)}^{[l]}}{\delta Z_{(0,0)}^{[l]}}$$
Let's generalize this and think about $$ Z_{j,i}^{[l]}$$.
$$ \frac{\delta J }{\delta Z_{(j,i)}^{[l]}} = \sum_{p=0} \frac{\delta J }{\delta A_{(k, i)}^{[l]}} \frac{\delta A_{(k,i)}^{[l]}}{\delta Z_{(j,i)}^{[l]}}$$

Considering only (j,i), vectorizing requires multiplying the vector by $$(1,n^{[l]}) (n^{[l]},1)$$. To generalize j and vectorize it, the rows of the matrix have a range of $$n^{[l]}$$. That is, the following derivation is derived:

$$ 
\begin{equation*}
\begin{bmatrix}
\frac {\partial J}{\partial z_{1, i}^{[l]}} \\
\vdots \\
\frac{ \partial J}{ \partial z_{j, i}^{[l]}} \\
\vdots \\
\frac{ \partial J}{ \partial z_{n^{[l]}, i}^{[l]}}
\end{bmatrix} =
\begin{bmatrix}
\frac{\partial a_{1, i}^{[l]}}{ \partial z_{1, i}^{[l]}} & \dots & \frac{\partial a_{j, i}^{[l]}}{ \partial z_{1, i}^{[l]}} & \dots & \frac{\partial a_{n^{[l]}, i}^{[l]}}{ \partial z_{1, i}^{[l]}} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
\frac{\partial a_{1, i}^{[l]}}{ \partial z_{j, i}^{[l]}} & \dots & \frac{\partial a_{j, i}^{[l]}}{ \partial z_{j, i}^{[l]}} & \dots & \frac{\partial a_{n^{[l]}, i}^{[l]}}{ \partial z_{j, i}^{[l]}} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
\frac{a_{1, i}^{[l]}}{ \partial z_{n^{[l]}, i}^{[l]}} & \dots & \frac{\partial a_{j, i}^{[l]}}{ \partial z_{n^{[l]}, i}^{[l]}} & \dots & \frac{\partial a_{n^{[l]}, i}^{[l]}}{ \partial z_{n^{[l]}, i}^{[l]}}
\end{bmatrix}
\begin{bmatrix}
\frac{ \partial J}{\partial a_{1, i}^{[l]}} \\
\vdots \\
\frac{ \partial J}{\partial a_{j, i}^{[l]}} \\
\vdots \\
\frac{ \partial J}{\partial a_{n^{[l]}, i}^{[l]}}
\end{bmatrix},
\end{equation*}
$$  

If we try to generalize about i, we can see that the row should change every time i changes. Therefore, it is difficult to completely vectorize a matrix, and vectorize it partially.

$$
\frac {\partial J}{\partial z^{[l]}} = \frac{\partial \vec{a}_{:, i}^{[l]}}{\partial \vec{z} _{:, i}^{[l]}} \frac{\partial J}{\partial \vec{a}_{:, i}^{[l]}}
$$
and ,
$$ \frac {\partial J}{\partial \vec{a}_{:, i}^{[l]}} \in \mathbb{R}^{n^{[l]}} , \frac {\partial \vec{a}_{:, i}^{[l]}}{\partial \vec{z}_{:, i}^{[l]}} \in \mathbb{R}^{n^{[l ]} \times n^{[l]}}$$
   It is included in the same real number set.
This can be finally summarized as follows.

$$


\frac{\partial J}{\partial \vec{Z}^{[l]}} =
\begin{bmatrix}
\frac{\partial J}{\partial \vec{z}_{:, 1}^{[l]}} & \dots & \frac{\partial J}{\partial \vec{z}_{:, i}^{[l]}} & \dots & \frac{\partial J}{\partial \vec{z}_{:, m}^{[l]}}
\end{bmatrix}
 

$$ 

## $$ \frac{\delta J }{\delta A^{[l-1]}} $$

This time, we will only think about A(0,0).
$$
\frac{\partial J}{\partial a_{0, 0}^{[l - 1]}} = \sum_j \frac{\partial J}{\partial z_{j, 0}^{[l]} } \frac{\partial z_{j, 0}^{[l]}}{\partial a_{0, 0}^{[l - 1]}} = \sum_j \frac{\partial J}{\partial z_{j, 0}^{[l]}} w_{j, 0}^{[l]}.
$$
You can see that sum is performed for j. This will be able to be expressed with vectorize later. Now, let's think about the generalized k,i.

$$
\frac{\partial J}{\partial a_{k, i}^{[l - 1]}} = \sum_j \frac{\partial J}{\partial z_{j, i}^{[l]} } \frac{\partial z_{j, i}^{[l]}}{\partial a_{k, i}^{[l - 1]}} = \sum_j \frac{\partial J}{\partial z_{j, i}^{[l]}} w_{j, k}^{[l]}.
$$

Considering only one unit of A, it can be viewed as the product of (1,n[l]) (n[l],1) vector. If we vectorize this for a random node, it can be derived as follows.

$$\begin{equation*}
\begin{split}
&
\begin{bmatrix}
\frac{\partial J}{\partial a_{1, 1}^{[l - 1]}} & \dots & \frac{\partial J}{\partial a_{1, i}^{[l - 1]}} & \dots & \frac{\partial J}{\partial a_{1, m}^{[l - 1]}} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
\frac{\partial J}{\partial a_{k, 1}^{[l - 1]}} & \dots & \frac{\partial J}{\partial a_{k, i}^{[l - 1]}} & \dots & \frac{\partial J}{\partial a_{k, m}^{[l - 1]}} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
\frac{\partial J}{\partial a_{n^{[l - 1]}, 1}^{[l - 1]}} & \dots & \frac{\partial J}{\partial a_{n^{[l - 1]}, i}^{[l - 1]}} & \dots & \frac{\partial J}{\partial a_{n^{[l - 1]}, m}^{[l - 1]}}
\end{bmatrix} \\
&=
\begin{bmatrix}
w_{1, 1}^{[l]} & \dots & w_{j, 1}^{[l]} & \dots & w_{n^{[l]}, 1}^{[l]} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
w_{1, k}^{[l]} & \dots & w_{j, k}^{[l]} & \dots & w_{n^{[l]}, k}^{[l]} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
w_{1, n^{[l - 1]}}^{[l]} & \dots & w_{j, n^{[l - 1]}}^{[l]} & \dots & w_{n^{[l]}, n^{[l - 1]}}^{[l]}
\end{bmatrix} \\
&\  \cdot
\begin{bmatrix}
\frac{\partial J}{\partial z_{1, 1}^{[l]}} & \dots & \frac{\partial J}{\partial z_{1, i}^{[l]}} & \dots & \frac{\partial J}{\partial z_{1, m}^{[l]}} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
\frac{\partial J}{\partial z_{j, 1}^{[l]}} & \dots & \frac{\partial J}{\partial z_{j, i}^{[l]}} & \dots & \frac{\partial J}{\partial z_{j, m}^{[l]}} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
\frac{\partial J}{\partial z_{n^{[l]}, 1}^{[l]}} & \dots & \frac{\partial J}{\partial z_{n^{[l]}, i}^{[l]}} & \dots & \frac{\partial J}{\partial z_{n^{[l]}, m}^{[l]}}
\end{bmatrix},
\end{split}
\end{equation*}

$$ 
This can be expressed as: 
$$

\frac{\partial J}{\partial \vec{A}^{[l - 1]}} = {\partial \vec{W}^{[l]}}^{T} \frac{\partial J}{\partial \vec{Z}^{[l]}}

$$

## Summary 

Fully Connected Network actually stores A[l-1], Z[l], W[l], and b[l] while performing forward propagation at the lth layer, and receives dA[l] as a parameter while performing backpropagation. . Then, the cached value and dA[l] are substituted into the derivation seen above, and the parameters are passed back to the next layer, the l-1 layer.