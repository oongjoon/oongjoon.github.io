---
title : "Batch Norm introduction"



excerpt: "Batch Norm intro"

categories:
  - Optimization
tags:
  - [Machine Learning,batch size,deep learning]
# classes : wide
toc: true
toc_sticky: true
---

## Similar to Input Normalization

The Input Normalization we saw earlier adjusts the range (scale) of the input so that the weight update is turned off uniformly. Therefore, it served to make the convergence relatively fast.

![Neural Network 적용 전에 Input data를 Normalize 해야 하는 이유](https://camo.githubusercontent.com/1f4da8632894c7220f804bcddc6e50bc708e6481/68747470733a2f2f692e696d6775722e636f6d2f32784e423261552e6a7067)

## Why use batch normalization?

Batch normalization applies this to the input of the hidden layer. This speeds up convergence and enables faster hyperparameter search.

## How to do Batch Norm

Let's take an example.

Find the mean and variance of the second hidden layer. 

$$ \Sigma z^{(2)}_{i} $$  

$$ \mu = \frac {\Sigma z^{(2)}_{i}} {m} $$ 

$$ \sigma = \frac {(\Sigma z^{(2)}_{i} - \mu )^2} {m} $$

$$ z_{norm}^{(i)}  =  \frac {(\Sigma z^{(2)}_{i} - \mu )}{\sqrt {\sigma} } $$

However, this will disable the calculation if $$\Sigma$$ becomes zero. So, the calculation is as follows.

$$ z_{norm}^{(i)}  =  \frac {(\Sigma z^{(2)}_{i} - \mu )} {\sqrt {\sigma^2 + \epsilon}  } $$

 

## Practical Application of Batch Norm

if you use $$ z_{norm}$$  as is , The distribution will follow N(0,1). However, we don't want all the distributions of z to follow N(0,1). By further developing 

$$ \widehat{z} = \alpha z_{norm} + \beta $$  

this expression is written like above. If you write like this   $$ \alpha =  {\sqrt {\sigma^2 + \epsilon}  }, \beta = \mu $$ . we use  $$ \alpha , \beta $$ as learnable parameters . So, the mean and variance follow the desired distribution. 

