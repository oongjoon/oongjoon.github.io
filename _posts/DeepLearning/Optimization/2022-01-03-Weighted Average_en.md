---
title : "Weighted Average and Momentum"



excerpt: "Practical batch norm in neural net"

categories:
  - Optimization
tags:
  - [Machine Learning,batch size,deep learning]
# classes : wide
toc: true
toc_sticky: true
---
This article is going to cover Exponentially Weighted Average and Momentum, which were covered in DLS. I reviewed it once again and wrote it as a Closed Book 3 days later.

## Exponentially  Weighted Average

DLS describes Exponentially Weighted Average as an example of calculating the average rainfall for the city of London.

![image](https://user-images.githubusercontent.com/50165842/147940628-ee8eafe9-4ec7-4d85-88bd-4051da795128.png)



$$ V_0 =  (1-\beta) \theta_0  $$

$$ V_1 =  \beta V_1 + (1 - \beta ) \theta_{0} $$

$$... $$

$$ V_n =  \beta V_n + (1 - \beta ) \theta_{n-1} $$  

![image](https://user-images.githubusercontent.com/50165842/147940701-fad10109-5d98-4c94-a202-9b47944b59f3.png)



The statistical significance of this is that V is an average that roughly reflects the number of $$1 \over {1-\beta} $$.
For example, if $$\beta$$ is 0.9, then V reflects the weather for 10 days.

Therefore, as $$\beta$$ increases, the graph becomes smooth, while as $$\beta$$ decreases, the number of reflected days decreases, showing a rapidly changing graph.

## Weighted Average in statistical view

f we expand the above expression

$$ V_n = {\beta}^n (1-\beta) \theta_0 +{\beta}^{n-1} (1 - \beta ) \theta_{1} + .. + (1 - \beta ) \theta_{n}$$

it' the result.

There are some exponentially decreasing graphs. The exponentially weighted average is the sum of the weather values multiplied by element wise.

And, Professor Andrew explains the reason for the average that roughly reflects the number of $$1 \over {1-\beta} $$,

Since $$ (1 - \epsilon) ^{1 \over \epsilon} = {1 \over e } $$ , it is said to reflect the latest data as much as $$ {1-\beta} $$. I've also tried calculating the geometric series, and a ratio of about 0.7 comes out, and the professor says it's the rule of thumb, but for now, I decided to just accept it.



## Gradient Descent Momentum

Comparing Weighted Average and Gradient Descent, Gradient Descent udpates the weight with only the instantaneous gradient.



$$dV = \beta dV + (1 - \beta ) dW $$

$$ W = : W - \alpha dV $$

Update proceeds through the same rule as above.

Professor Andrew

> You said that it is similar to the law of physics, where friction is applied to the existing running speed (dV) and ($$\beta$$ ) is accelerated as much as the force is applied ( $$(1 - \beta ) dW $$).

I really liked this explanation.

### omit learning rate

$$dV = \beta dV + \alpha dW $$

$$ W = : W - \alpha dV $$

It is said that the use of the above expression by omitting the learning rate is also shown in the paper. Even in the deeplearning book, I know that the learning rate is explained by omitting the weight udpate. There is no detailed explanation for this, but here are my thoughts:

  $$dV = \beta dV + dW $$

$$\alpha dV = \alpha \beta dV + \alpha dW $$

$$ dV \approx \alpha \beta dV + \alpha dW $$

$$ W = : W - dV $$

(I'm not sure. If anyone knows, please let me know in the comments or e-mail.)

### implementation in deep learning

If the above expression is applied as it is when implementing the actual implementation, the initial value will be smaller than the expected value because $$ V_0 = 0$$ is initialized and started. Therefore, scaling is performed by dividing by $$ 1 \over 1 - {\beta}^t $$. As t increases, the scaling factor approaches 1. This is because the weighted average reflects the latest data as many as $$1 \over {1-\beta} $$ .

### gradient descent vs gradient decent momentum

You can use gradient descent to reflect the latest data like momentum. For example, if $$\beta$$ is 0.99, you can put gradients for 100 data in memory. However, momentum can solve this with one line of code. Although statistics are not essential, I felt that it could be a good tool in advancing algorithms.

