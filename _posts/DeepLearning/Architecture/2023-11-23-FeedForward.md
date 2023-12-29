---
title : " Let's define the general term of the FeedForward Network with an arbitrary number of parameters and derive the vectorization process using a formula.  "



excerpt: "Mathematical view"

categories:
  - DLArchitecture
tags:
  - [Machine Learning,deep learning ]
# classes : wide
toc: true
toc_sticky: true
usemathjax : true
---


## Why we need understand Neural Network in Mathematical view?
FeedForward Network is the most basic form of neural net. This is implemented by multiplying the nodes by the weight matrix and adding a bias. Because understanding a neural net as a formula increases your understanding of the neural net, it is believed that understanding it mathematically is very helpful in developing the capabilities of a deep learning engineer. In this article, we will derive the general term of the node of an arbitrary layer and use a formula to describe the process of vectorizing it.
## Term Definition
Here, the Symbol and Notation methods for Term were referenced from Professor Andrew's DeepLearning lecture.

|Symbol|Description|
|----------------------------------------------|----------------- -------------------------------------------------- ----------------|
|$$l$$| $$l $$th layer. $$ l $$ = 0, ... , L means the number of the layer with weights and bias.|
|$$n^{[l]}$$|   the number of nodes in the $$ l $$ th layer.|
|$$j$$|$$j = 0,..., n^{[l]}$$|
|$$k$$|$$k = 0, .... n^{[l-1]} $$|
|$$m$$|$$m$$ is the batch size in the training step.|
|$$i$$|$$i = 0,..... m$$|
|$$w_{j,k}^{[l]}$$| Weight of the $$l $$th layer $$ W^{[l]} $$ , $$ W^{[l]} \in \mathbb{R}^{n^{[l]} \times n^ {[l-1]} } $$ . It means the $$(j,k) $$ element of $$ W^{[l]} $$.|
|$$z_{j,i}^{[l]}$$| This is the $$(j,i)$$ component of the output that adds the bias of the $$l$$th layer. |
|$$a_{j,i}^{[l]}$$|$$l$$ This is the $$(i,j) $$ component of the activation of the th layer.|
|$$b^{[l]}$$|$$l$$ This is the bias of the first layer.$$ b^{[l]} \in \mathbb{R}^{n^{[l]} } $$ |
|$$g_{j}^{[l]}$$| $$l$$th layer activation function $$g_{j}^{[l]} : \mathbb{R}^{n^{[l]}} \rightarrow \mathbb{R}^{n^{[ l]}} $$ , $$g_{j}^{[l]} \in \mathbb{R}^{n^{[l]} } $$|

## Explanation in Mathematical View
![image](https://kau365-my.sharepoint.com/personal/oongjoon_kau_kr/Documents/%EB%B8%94%EB%A1%9C%EA%B7%B8%EC%9D%B4%EB%AF%B8%EC%A7%80/DeepLearning/Architecture/feedforward%20-%20%EB%B3%B5%EC%82%AC%EB%B3%B8.png)

If you look at other educational materials, they limit the explanation to three nodes for ease of explanation. However, here, in order to define the general expression, we explain the $$ l$$th layer that converts $$ n^{[l-1]}$$ nodes into $$ n^{[l]}$$ nodes.  (Bias is omitted only in the figure.) . The picture above shows the $$ l$$th layer. The circle is the node and the connecting line is the weight. For notation, please refer to [Definition](#term-definition).

### $${Z}^{[l]}$$ 's vectorization process

Here, $$n^{[l-1]} $$ nodes are connected to $$z_{k,i}^{[l]}$$.
thus ,
$$ 
\begin{equation}
z_{j,i}^{[l]} = \sum_{k=0}^{n^{[l-1]}}  w_{j,k}^{[l]} \cdot a_{k,i}^{[l-1]} + b_{j}^{[l]} 
\end{equation}
$$    

Let’s call the above formula `(1)`.

Vector space is defined as follows: $$ \vec{a}_{:, i}^{[l-1]} \in \mathbb{R}^ {n \times {n^{[l-1]}} }, \vec{w }_{j, :}^{[l]} \in \mathbb{R}^ {n \times {n^{[l-1]}}} $$ .

In deep learning, this multiplication is performed in parallel rather than sequentially (ex.Numpy). Therefore, we must vectorize `(1)`. The above expression represents sum, but it can be viewed as a multiplication of vector $$ w_{j,:}^{[l]} $$ and vector $$ a_{:,i}^{[l-1]}$$ there is. In the above expression, the range of variable j is $$ 0<= j <=n^{[l]} $$. Therefore, by extending this, you can vectorize it as follows.


$$
\begin{align*}
\begin{bmatrix}
z_{1, i}^{[l]} \\
\vdots \\
z_{j, i}^{[l]} \\
\vdots \\
z_{n^{[l]}, i}^{[l]}
\end{bmatrix} &=
\begin{bmatrix}
w_{1, 1}^{[l]} & \dots & w_{1, k}^{[l]} & \dots & w_{1, n^{[l - 1]}}^{[l]} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
w_{j, 1}^{[l]} & \dots & w_{j, k}^{[l]} & \dots & w_{j, n^{[l - 1]}}^{[l]} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
w_{n^{[l]}, 1}^{[l]} & \dots & w_{n^{[l]}, k}^{[l]} & \dots & w_{n^{[l]}, n^{[l - 1]}}^{[l]}
\end{bmatrix}
\begin{bmatrix}
a_{1, i}^{[l - 1]} \\
\vdots \\
a_{k, i}^{[l - 1]} \\
\vdots \\
a_{n^{[l - 1]}, i}^{[l - 1]}
\end{bmatrix} +
\begin{bmatrix}
b_1^{[l]} \\
\vdots \\
b_j^{[l]} \\
\vdots \\
b_{n^{[l]}}^{[l]}
\end{bmatrix},
\end{align*}
$$  

This can be expressed in a formula as follows:

$$\vec{z}_{:, i}^{[l]} = \vec{W}^{[l]} \vec{a}_{:, i}^{[l - 1]} + \vec{b}^{[l]} $$

The vector space is defined as .$$\vec{z}_{:, i}^{[l]} \in \mathbb{R}^{ {n^{[l]}}} , \vec{W}^{[l]} \in \mathbb{R}^{n^{[l]} \times n^{[l - 1]}}  , \vec{b}^{[l]} \in \mathbb{R}^{ {n^{[l]}}} , \vec{a}_{:, i}^{[l - 1]} \in \mathbb{R}^{ {n^{[l - 1]}}}$$




This is a math expression for one training data. Now, let's vectorize this by expanding it to the size of $$ m $$ batch data.

$$
\begin{align}
\vec{Z}^{[l]} &=
\begin{bmatrix}
\vec{z}_{:, 1}^{[l]} & \dots & \vec{z}_{:, i}^{[l]} & \dots & \vec{z}_{:, m}^{[l]}
\end{bmatrix}  \\
&= \vec{W}^{[l]}
\begin{bmatrix}
\vec{a}_{:, 1}^{[l - 1]} & \dots & \vec{a}_{:, i}^{[l - 1]} & \dots & \vec{a}_{:, m}^{[l - 1]}
\end{bmatrix} +
\begin{bmatrix}
\vec{b}^{[l]} & \dots & \vec{b}^{[l]} & \dots & \vec{b}^{[l]}
\end{bmatrix} \notag \\
&= \vec{W}^{[l]} \vec{A}^{[l - 1]} + broadcast(\vec{b}^{[l]}), \notag \\
\end{align}
$$  

The vector space is defined as follows .$$ \vec{Z}^{[l]} \in \mathbb{R}^{n^{[l]} \times m} , \vec{A}^{[l - 1]} \in \mathbb{R}^{n^{[l - 1]} \times m}$$


### $${A}^{[l]}$$ 's vectorization process


When $$ Z^{[l]}$$ is calculated, it is passed as a parameter to $$g_{j}^{[l]}$$ for calculation. It can be expressed in the following way. 

$$ 
\begin{equation}
a_{j, i}^{[l]} = g_j^{[l]}(z_{1, i}^{[l]}, \dots, z_{j, i}^{[l]}, \dots, z_{n^{[l]}, i}^{[l]}). 
\end{equation}
$$  

Let’s call the above formula `(2)`.

Likewise, since it proceeds in parallel rather than sequentially, let's vectorize `(2)`.  

$$
\begin{align*}
\begin{bmatrix}
a_{1, i}^{[l]} \\
\vdots \\
a_{j, i}^{[l]} \\
\vdots \\
a_{n^{[l]}, i}^{[l]}
\end{bmatrix} &=
\begin{bmatrix}
g_1^{[l]}(z_{1, i}^{[l]}, \dots, z_{j, i}^{[l]}, \dots, z_{n^{[l]}, i}^{[l]}) \\
\vdots \\
g_j^{[l]}(z_{1, i}^{[l]}, \dots, z_{j, i}^{[l]}, \dots, z_{n^{[l]}, i}^{[l]}) \\
\vdots \\
g_{n^{[l]}}^{[l]}(z_{1, i}^{[l]}, \dots, z_{j, i}^{[l]}, \dots, z_{n^{[l]}, i}^{[l]}) \\
\end{bmatrix}
\end{align*}
$$
  
Expressing this in a formula  

$$
\vec{a}_{:, i}^{[l]} = \vec{g}^{[l]}(\vec{z}_{:, i}^{[l]})
$$  

The vector space is defined as .$$ \vec{a}_{:, i}^{[l]} \in R^{n^{[l]}} $$

The above formula refers to one node among all activations. Let's expand this to the entire activation. 

$$
\vec{A}^{[l]} =
\begin{bmatrix}
\vec{a}_{:, 1}^{[l]} & \dots & \vec{a}_{:, i}^{[l]} & \dots & \vec{a}_{:, m}^{[l]}
\end{bmatrix},
$$  

Vector space is defined as follows: $$ \vec{A}^{[l]} \in R^{n^{[l]} \times m} $$

## Conclusion

We directly derived the vectorization process of an arbitrary layer mathematically. I think it will be helpful for the following reasons.

1. Increases understanding of neural net.
A deep understanding of how neural nets work will help you improve model performance and solve problems. Deriving the general terms allows us to better understand the inner workings of a neural net.

2. Understand how neural nets optimize calculations
Deriving the vectorization process as a formula can provide insight into how it can be optimized.

3. Problem-solving skills
Deriving the vectorization process as a formula will help you diagnose and troubleshoot problems when your model does not behave as expected. This is very useful during debugging and optimization.\

4. Improve communication skills
The ability to derive and understand these equations is important in communicating with other engineers, researchers, and stakeholders. This helps you communicate complex concepts clearly and promote team collaboration.

5. It is the most basic.
Since the feedforward network is the most basic form, deriving the vectorize process using a formula will also be helpful in deriving other neural nets. In addition, when decomposing other neural net models, feedforward is often used.




## Reference

1.  [feedforward-neural-networks-part-1/journalsim From Jonas Lalin ](https://jonaslalin.com/2021/12/10/feedforward-neural-networks-part-1/)    

2. [wikiepdia](https://en.wikipedia.org/wiki/Feedforward_neural_network)
