---
title : "Batch Norm in Neural Network Training and Test"



excerpt: "Practical batch norm in neural net"

categories:
  - Optimization
tags:
  - [Machine Learning,batch size,deep learning]
# classes : wide
toc: true
toc_sticky: true
---

## Batch Norm in Neural Network Training and Test

In this article, we will learn how to apply Batch Norm in Neural Network Training and Test.

## Adding Batch Norm into Nueral Network

$$ X^{[1]}   \implies W^{[1]} , b^{[1]} \implies Z^{[1]} ,  a^{[1]} =  g(Z^{[1]}) \implies W^{[2]}  ,b^{[2]}  \implies Z^{[2]} , a^{[2]} = g(Z{[2]}) \implies  ... $$ 

Although [] is a notation that expresses the hidden layer, I think you will understand if you read the previous post.😀

This is a typical Neural Net feedforward. So what if we add the batch norm??



$$ Z^{[1]} ,  \mu =  \frac {\Sigma z^{[2]}}{m}, \sigma = \frac{(\Sigma z^{[2]} - \mu )^2}{\sqrt {\mu}}  \implies {z_{norm}}^{[1]} = \frac {(\Sigma z^{[1]}  -  \mu )}{\sqrt {\sigma}} \implies  $$

$$ {z_{norm}}^{[1]} = \frac {(\Sigma z^{[1]} - \mu)} {\sqrt {\sigma^2 + \epsilon }} \implies \widehat{z} = \alpha z_{norm} + \beta  $$    



New learnable parameters  $$\alpha , \beta $$  are appended as above .  In typically , there are parameters like  $$W^{[1]} ,b^{[1]} ,W^{[2]} , b^{[2]}  ..$$ . But , parameters like $$ {\alpha}^{[1]}  {\beta}^{[1]} , {\alpha}^{[2]} , {\beta}^{[2]} ..$$ are appended. 

## Learnable parameters in Neural Network

Here, the question to consider is whether all these parameters are needed. Let's look at the formula below. 

 $$ Z_{norm} = \frac {(\Sigma z - \mu)} {\sqrt {\sigma^2 + \epsilon}} $$ is the forumla that calculates znorm . To find the znorm, the mean is subtracted.  $$ \mu = \frac {\Sigma z }{m}  = \frac{\Sigma {(wx^T + b)} }{m} \implies Z_{norm} = \frac {\Sigma {wx^T}}{\sqrt {\sigma^2 + \epsilon}}$$ .  That is, the bias term has no effect. Therefore, deleting this bias term has no effect.



Let's look at the training pseudo code using batch norm.

```python
for epoch = 1 ... epochs :
	for  i=1 ... mini-batch :
		feedforward , not using bias term
        	(in each hidden layer replace z_norm with z_hat using alpha, beta )
        calculate db , dw ,da 
        b ,w ,a update using opitmization algorithm(e.g. Adam, RMSProp , SGD ,GD etc)
```





## Batch Norm at Test time

If you understand the theory of batch norm, you can see that you need to find the mean and variance of each batch. But, what to do at test time? There is only one input. Do I need to find the mean and variance for it? You know this doesn't make sense. In general, in neural networks, when training in an existing algorithm,  we estimate $$ \mu , \sigma $$ using  exponentially weighted average .  At that time , $$ \mu , \sigma $$ use each hidden layer's mean ,variance. Then , we update  $$ \mu , \sigma $$ per every mini-batch. This will give you an approximate value of $$\mu , \sigma$$ .  In other words, in train, the mean and variance are calculated through mini-batch. However, there may be cases where only one test needs to be processed, and the mean and variance estimates are used to process a single example. ***<u>This does not calculate the exact mean and variance, but it is quite robust compared to finding the exact mean.</u>*** However, in reality, such calculations may be implemented differently in deep learning frameworks. But, it will work just fine anyway