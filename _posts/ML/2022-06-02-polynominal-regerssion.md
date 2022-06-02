---
title : "Polynomial Regression"



excerpt: "Standford ML "

categories:
  - StandfordML
tags:
  - [Machine Learning,LinearRegression , StandfordML]
# classes : wide
toc: true
toc_sticky: true
---

# Polynomial Regression in Multiple Features

A linear regression model represented by one independent variable x and one dependent variable y is called Simple Linear Regression. Expressing this as a formula
$$
y = \theta_{1} x + \theta_{0}
$$

This is easy to understand because it can be simply expressed as a graph with x and y axes. However, when building a linear regression model that needs to predict the dependent variable y, there may not be only one independent variable. In this case,we need  a different equation.

## What is Polynomial Regression

Polynomial regression is a linear regression model with multiple independent variables instead of one.

This can be expressed in the following way.


$$
\begin{align}
y &= \theta_{0} + \theta_{1}x_{1} + \theta_{2}x_{2} + ... + \theta_{n}x_{n} \\
y &= \sum_{i=1}^{n} \theta_{i}x_{i} + \theta_{0} \\
\end{align}
$$

The point to note here is that not all features are used, but only as many features as necessary.

![image](https://user-images.githubusercontent.com/50165842/170036215-31ef848c-168f-4f1d-9f17-e7c39509d458.png)

For example, if data is given as above, it is sufficient to use only one feature.

![image](https://user-images.githubusercontent.com/50165842/170036527-36e28dc6-2dea-4dab-8e9e-affe47055672.png)

Given this data, if you use two features (x, x*x), you will get a model like red. This is a model that does not represent data well.

![image](https://user-images.githubusercontent.com/50165842/170037696-19d8cc0f-6b60-406e-b677-682a83f1e860.png)



If 3 features are used (x,x*x,x*x*x), it will become a cubic function, making a model like the one above.



![image](https://user-images.githubusercontent.com/50165842/170037425-e9b1f125-6509-4e66-ae16-905a7445b020.png)

However, if the features are used differently (x,x*x x*(1/2) (root x) ), it will become a model that increases gradually as above and becomes a model that expresses data well.



In the case of the above data, even if a feature is given as many as x1,x2...xn, it can be expressed using only an arbitrary feature xk, and the data can be expressed well depending on how the index of this feature is set.

## Learning Polynomial Regression

After setting up the model, we need to train the model. Polynomial regression model is also trained using gradient descent.

### Gradient Descent in Multiple Variables

First, if we write the gradient descent in the existing linear regression as an equation, it is as follows.
$$
\begin{align}
\theta_{0} := \theta_{1} - \alpha * {\partial J(\theta) \over \partial\theta_{0}} \\
\theta_{1} := \theta_{1} - \alpha * {\partial J(\theta) \over \partial\theta_{1}}

\end{align}
$$
In the case of polynomial regression, the number of variables increases, so you can update it several times.
$$
\begin{align}
\theta_{0} &:= \theta_{1} - \alpha * {\partial J(\theta) \over \partial\theta_{0}} \\
\theta_{1} &:= \theta_{1} - \alpha * {\partial J(\theta) \over \partial\theta_{1}} \\
\theta_{2} &:= \theta_{2} - \alpha * {\partial J(\theta) \over \partial\theta_{2}} \\
\theta_{3} &:= \theta_{3} - \alpha * {\partial J(\theta) \over \partial\theta_{3}} \\
&...


\end{align}
$$
If we generalize this, we get the following eqation:
$$
\begin{align}

\theta_{k} &:= \theta_{k} - \alpha * {\partial J(\theta) \over \partial\theta_{k}} \\

(k &= 1 ... n)

\end{align}
$$


### Convergence Speed

We have seen how to train a polynomial regression model. You can start training right away, but the faster the model converges, the less time it takes to train the model. The convergence time of this model can be accelerated by several hyperparameters.

#### Scaling Variables

![image](https://user-images.githubusercontent.com/50165842/170268003-1fd87952-68ed-492f-9ba4-e0169e7117ae.png)

If the scale difference between Variables is large, the Loss Function will show the above form. When the parameter of a variable with a large scale is updated, the value of the loss function changes relatively large, and when the parameter of a variable with a small scale is updated, the value of the loss function changes relatively small.

#### Learning Rate

$$
\begin{align}

\theta_{k} &:= \theta_{k} - \alpha * {\partial J(\theta) \over \partial\theta_{k}} \\

(k &= 1 ... n)

\end{align}
$$

When updating the model's weight, the term $$\alpha$$ is multiplied. This value is called the learning rate. If this learning rate value is large, the value of the loss function will converge as follows. The convergence value will not decrease uniformly.



![image](https://user-images.githubusercontent.com/50165842/171631540-d4abe530-7f8d-4327-bc79-462b28a0f437.png)

If the learning rate value is small, it will take a long time for the value of the loss function to converge.

![image](https://user-images.githubusercontent.com/50165842/171632233-0ca16407-0ddd-4db1-903e-ea386539c8d6.png)

If the learning rate value has an appropriate value, the value of the loss function will converge relatively well.



![image](https://user-images.githubusercontent.com/50165842/171632544-af33dc99-875b-4b16-899f-62bbd3e2da6f.png)

In other words, an appropriate learning rate value is a value that decreases the loss function value for every update, but not decrease the function too small.



# References

Standford-ml [기계 학습  Coursera](https://www.coursera.org/learn/machine-learning)





