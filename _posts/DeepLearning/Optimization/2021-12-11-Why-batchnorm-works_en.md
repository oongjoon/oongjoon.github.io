---
title : "Why Batch Norm works"



excerpt: "Batch Norm why "

categories:
  - Optimization
tags:
  - [Machine Learning,batch size,deep learning]
# classes : wide
toc: true
toc_sticky: true
---
This article was written after listening to Coursera's batch norm lecture. Rather than summarizing the lecture content as it is, it is an article expressed in my own words.



## Why batch norm works

In the last article, I wrote about how to implement Batch Norm and set what learnable parameters. This article will look at why the batch norm works at a more low-level level.

## Covariance shift

![image](https://user-images.githubusercontent.com/50165842/145677607-cca1342b-037f-4857-8cf5-e592cd38d6b8.png)



Let's assume the scenario where you create an 'image classification model to classify cats. Our train dataset only contains pictures of black cats.

![image](https://user-images.githubusercontent.com/50165842/145677619-f0fcd942-ff8d-4518-b5a4-1789736b05a8.png)

However, the test dataset contains colorful cat pictures.

![image](https://user-images.githubusercontent.com/50165842/145677666-4cd7f905-3735-4a87-b0bb-9694d70c5d18.png)

Even if the model works well on the train, it is likely not so well on the test. ***<u>This is likely to be difficult to classify in the test because the distribution of the data is different.</u>*** In this case, it may be necessary to retrain the model from scratch. Even if the ground truth does not change, we will have to re-learn it. ***<u>we call these distribution difference as "covariance shift"</u>***

## Batch Norm In Neural Net

![image](https://user-images.githubusercontent.com/50165842/145677792-35461997-97df-4974-b98f-0f1b7b585538.png)

There is the output of the second hidden layer, $$ a^{[2]} $$. ***<u>From the perspective of all 3rd, 4th, and 5th hidden layers, we update the parameters of the following hidden layers so that they can generalize well to the input</u>*** $$ a^{[2]} $$.

![image](https://user-images.githubusercontent.com/50165842/145678460-dcf38ef3-1bf6-43b5-8af1-913b8511740e.png)

However, the parameters are updated through the learning of the hidden layer of the first and second layers. That is since the distribution of $$ a^{[2]} $$ changes every time, from the perspective of the whole Neural Net, generalization is attempted on a new input every time. That is, a covariance shift occurs. Therefore, the mean and variance are unified through the batch norm to enable faster convergence. Again, mean and variance do not always follow N(0,1) after normalization. $$ Using the learnable parameters of \alpha and \beta $$, mean and variance are also updated with values that generalize well.



## What Batch Norm Essentially Does 

Batch Norm essentially reduces the amount of change in w2 and b2. ***<u>We limit the range of</u>*** $$ a^{[2]} $$ ***<u>by specifying</u>*** $$ \alpha 2 , \beta 2$$ .  By limiting the range that $$ a^{[2]}$$ can go, we can train neural nets more reliably. In other words, it reduces the amount by which the distribution shifts. I***<u>t can be seen that the previous hidden layers reduce the amount of learning for subsequent hidden layers.</u>*** ***<u>In other words, w3 and b3 learn about</u>*** $$ a^{[2]} $$ ***<u>independently from other layers.</u>*** It becomes independent from the perspective of other layers. As a result, it speeds up the convergence of the network. When Batch Norm is not applied, convergence does not work well, ***<u>so you may think that it is a parameter that affects performance. But, it's not true.</u>***

## Unexpected regularization

When Batch Norm is applied in Neural Net, unintentional regularization is applied. Batch Norm is literally a method of updating the mean and variance after calculating the gradient for a mini-batch. However, since it is a mini-batch rather than the entire data, ***<u>some noise is added to the mean and variance compared to the mean and variance of the entire data.</u>*** Since a little noise of the mean and variance of the entire train dataset is added, the effect of regularization occurs . It is similar to regularization by adding some noise to the hidden layer output each time with dropout. ***<u>However, it should be noted that the batch norm should not be used for regularization because it is literally unexpected regularization.</u>***

 