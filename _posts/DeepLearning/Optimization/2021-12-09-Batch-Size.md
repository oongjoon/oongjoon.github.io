---
title : "Batch vs Mini batch vs Stochastic"



excerpt: "Batch size"

categories:
  - Optimization
tags:
  - [Machine Learning,batch size,deep learning]
# classes : wide
toc: true
toc_sticky: true
---
This is the content I organized after listening to the DeepLearning Specialization and BoostCourse AI tech 2nd term.

## What is Batch Size?

Batch size is a hyperparameter that determines how much data to update by calculating loss when performing optimization with GD (gradient descent).Depending on how much this batch size is given, each has its own advantages, disadvantages and features. We would like to find out about this.

## Batch vs Mini-Batch

Let's look at the difference between Batch and Mini-Batch.

$$ X = {X^{(1)} , X^{(2)} , X^{(3)} ...  X^{(1000)} ,X^{(1001)} , .... X^{(m)}} $$ 

Existing batch GD performed gradient descent after calculating the loss for m samples.

$$ X^{\left\{ 1 \right\}} = {X^{(1)} , X^{(2)} , X^{(3)} ...  X^{(1000)} } $$

However, in mini-batch, gradient descent is performed for 1000 samples. For example, if m is 5000000 pieces, then Batch calculates a loss for 5000000 pieces, and mini-batch calculates a loss for 1000 pieces.

Let's write mini-batch GD in pseudo code.



```
for t in range(5000) :
	for X(1) , X(2) ... X(t) in  X{t} :
		feedforwarding
    loss
    back_propagtion
    ## feedforwarding은 vectorization으로 한번에 계산이 된다.
   
	
```

This is how it goes.

## stochastic batch GD vs mini-batch GD vs batch GD

![image](https://user-images.githubusercontent.com/50165842/145401371-5219e32d-0a70-470f-a86b-942ed5eb87bc.png)

This is an extreme example showing the difference between Batch GD and Mini-batch GD. When using Mini-Batch GD, the cost function may oscillate. Because, because it is sampling a part of the whole data, the mini-batch may be chosen as a bad example. Since more noise than batch GD, the cost function is inevitably unstable.

![image](https://user-images.githubusercontent.com/50165842/145404443-a2506483-9f52-4090-8909-49582b7cf636.png)

- Stochastic gradient descent
  - GD with batch size of 1
  - No speed advantage with vectorization
- mini-batch gradient descent
   - Use of data smaller than the total training data size m
   - It is possible to utilize the speed advantage using vectorization
   - It is possible to update the entire training dataset without feedforward.
- Batch
   - Designate the entire training data size m as the batch size
   - Since the entire data is updated, the iteration interval of each update is long.



## mini-batch gradient descent in mathmatical view

Looking at the mini-batch from a mathematical point of view shows that convergence to the local-minima does not occur. This means that it can be used in non-convex objective functions.

![image](https://user-images.githubusercontent.com/50165842/145405606-66576664-2691-4da2-a083-8a249494df1c.png)

We will use the expected value of the loss using some parameters rather than all parameters.

![image](https://user-images.githubusercontent.com/50165842/145405896-6515bed6-5bc0-4577-aba6-5fc449739acc.png)

In the case of a mini-batch, the objective function changes each time because a different mini-batch is selected each time. This prevents convergence to the local optima.



