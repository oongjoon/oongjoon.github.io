---
title : "Optimization Assignment from scratch"



excerpt: "DeepLearning.ai Dls Optimization "

categories:
  - DLS_C2
tags:
  - [Machine Learning,Coursera,deep learning , C2 ]
# classes : wide
toc: true
toc_sticky: true
---
I think the DLS task is good for learning the overall flow and key points while filling in the blanks. However, when reviewing the same task, the purpose is to grasp all the details, so not only the blank space given by TODO, but the entire module was implemented from scratch. In the process, I felt many things.

# Gradient Descent

```python
X = data_input
Y = labels
parameters = initialize_parameters(layers_dims)
for i in range(0, num_iterations):
    # Forward propagation
    a, caches = forward_propagation(X, parameters)
    # Compute cost.
    cost += compute_cost(a, Y)
    # Backward propagation.
    grads = backward_propagation(a, caches, parameters)
    # Update parameters.
    parameters = update_parameters(parameters, grads)
```

This is pseudo code implementing Gradient Descent. I couldn't think of the part that uses cache in the first place.

## why cache?

Why is it that when you think of the equation of gradient descent, you use the chain rule to find the derivative of the l layer. So, we have to recursively create a call frame every time from loss.

```python
def backward_propagation(X, Y, cache):
    """
    Implement the backward propagation presented in figure 2.
    
    Arguments:
    X -- input dataset, of shape (input size, number of examples)
    Y -- true "label" vector (containing 0 if cat, 1 if non-cat)
    cache -- cache output from forward_propagation()
    
    Returns:
    gradients -- A dictionary with the gradients with respect to each parameter, activation and pre-activation variables
    """
    m = X.shape[1]
    (z1, a1, W1, b1, z2, a2, W2, b2, z3, a3, W3, b3) = cache
    
    dz3 = 1./m * (a3 - Y)
    dW3 = np.dot(dz3, a2.T)
    db3 = np.sum(dz3, axis=1, keepdims = True)
    
    da2 = np.dot(W3.T, dz3)
    dz2 = np.multiply(da2, np.int64(a2 > 0))
    dW2 = np.dot(dz2, a1.T)
    db2 = np.sum(dz2, axis=1, keepdims = True)
    
    da1 = np.dot(W2.T, dz2)
    dz1 = np.multiply(da1, np.int64(a1 > 0))
    dW1 = np.dot(dz1, X.T)
    db1 = np.sum(dz1, axis=1, keepdims = True)
    
    gradients = {"dz3": dz3, "dW3": dW3, "db3": db3,
                 "da2": da2, "dz2": dz2, "dW2": dW2, "db2": db2,
                 "da1": da1, "dz1": dz1, "dW1": dW1, "db1": db1}
    
    return gradients

```

This is the backpropagation code provided by DLS. You can see that it unpacks the output of all layers from the cache at once and computes the derivative using the chain rule, but does not create any more stack frames.

## Any other way?

If you follow the OOP programming style, you will be writing recursive. As many stack frames are generated, resource allocation is bound to increase. I thought that it would be ok to implement this simply if it is a layer of layer 3 revision. (I will try to implement this part in OOP style and add it again.)

# Mini Batch

If you use mini batch, you will use shuffle. It can be said that more general data can be learned by simply shuffled because it can learn in a random order, but let's break it down a little more and look into it in detail.

The mathematical implication of mini batch is that the loss function changes with every iteration. Unlike batch gradient descent, it has to be different to calculate the loss function with a part of the data. The shuffle goes further than this and shuffles it every epoch so that it computes a different loss function every epoch. This makes the pattern harder to learn, making it more likely to escape from the local minima and reach the global minima.

## Issue1 How to implement Shuffle in code?

### sol1

As I was trying to implement shuffle in code, I was worried about how to do it. The most basic thing to do is that the one-to-one function relationship should not change even if the X,Y pair is shuffled.

```python
np.random.permutation(X,axis=1)
np.random.permutation(Y,axis=1)
```

I initially tried like this. The reason for giving the option with `axis = 1` is that `axis=0` is the dim of the feature and `axis=1` indicates the data size, so permuation was performed based on `axis=1`.

However, here, even if the seed value is fixed at the beginning, it was confirmed that the seed changes to a different value after permutation once. So, I had to change the code like below.

```python
np.random.seed(0)
np.random.permutation(X,axis=1)
np.random.seed(0)
np.random.permutation(Y,axis=1)
```

### sol2

```
random_index = list(np.random.permutation(num_examples))
```

This time, I tried to create an index as much as the data sample size.

```
    for i in range(iter_nums) :
      shuffled_X =np.empty((nx,mini_batch_size))
      shuffled_Y = np.empty((1,mini_batch_size))
      for k,j in enumerate(random_index[i * mini_batch_size:(i+1) * mini_batch_size]) :
        
        shuffled_X[:,k] = X[:,j]
        shuffled_Y[:,k] = Y[:,j]
```

And, after creating an empty array with the same shape as the data each time, indexing the data with an element at a random index, copying it, and then copying it back to the emtpy array was used.

```
    for k ,j in enumerate(random_index[iter_nums * mini_batch_size : ]) :
        # print(k)
        shuffled_X[:,k] = X[:,j]
        shuffled_Y[:,k] =Y[:,j]
    
```

In the last batch, the calculation was performed by separately designating the base case. However, it has been confirmed that copy and paste occur frequently this way.

### sol3

The DLS solution was similar to mine, but the code was written more efficiently.

```
    # Step 1: Shuffle (X, Y)
    permutation = list(np.random.permutation(m))
    shuffled_X = X[:, permutation]
    shuffled_Y = Y[:, permutation].reshape((1, m))
    
```

I made a copy with a single indexing each time. However, in this solution, the entire sequence was indexed with an index in a random random order. It was the first time I knew that such an operation was supported in Numpy. I thought that Numpy's indexing could be understood as indexing rows and columns based on `,`. Don't forget that broadcasting is also supported in rows and columns.

```
    for k in range(0, num_complete_minibatches):
        # (approx. 2 lines)
        # mini_batch_X =
        # mini_batch_Y =
        # YOUR CODE STARTS HERE
        mini_batch_X = shuffled_X[:,k*mini_batch_size : (k+1) * mini_batch_size]
        mini_batch_Y = shuffled_Y[:,k*mini_batch_size : (k+1) * mini_batch_size]
        
```

Therefore, it was confirmed that indexing is relatively simple even when creating a mini_batch.

## Can't you make the Issue2 mini-batch a PYthon list?

I suddenly thought that it is not possible to make a mini-batch into a python list. This question arose when I first tried with a list, but got a test error saying that it is not a numpy array and changed it to a numpy array again. The answer to this is that it must be a numpy array.

Because, list in python performs operations by iteration. However, in a neural net, it is matrixed and parallel operation is performed. In other words, vectorization is performed to perform operations in parallel. numpy array supports matrix operation, python list does not support matrix operation. So, you have to use numpy array.

## Issue3. Shouldn't append operation be used when creating mini_batch?

I made numpy_list an empty list and did np.append() , but the shape came out different from the expected value. The reason is, it was written in the docs that if you do not specify an axis for np.append, the input will be flattened.

## Issue4. Axis is specified and np.append is executed, but dim error occurs

When doing np.append, a dim error occurred. The reason is that the dimension of the empty list cannot be set. Therefore, I thought that there was only one way to create a matrix of the same shape as that of the mini-batch and to create mini-batch data through assignment.

# Mini batch unit test

```
assert n_batches == math.ceil(m / mini_batch_size), f"Wrong number of mini batches. {n_batches} != {math.ceil(m / mini_batch_size)}"
for k in range(n_batches - 1):
    assert mini_batches[k][0].shape == (nx, mini_batch_size), f"Wrong shape in {k} mini batch for X {mini_batches[k][0].shape}"
    assert mini_batches[k][1].shape == (1, mini_batch_size), f"Wrong shape in {k} mini batch for Y {mini_batches[k][1].shape}"
    assert np.sum(np.sum(mini_batches[k][0] - mini_batches[k][0][0], axis=0)) == ((nx * (nx - 1) / 2 ) * mini_batch_size) , "Wrong values. It happens if the order of X rows(features) changes"
    
if ( m % mini_batch_size > 0):
    assert mini_batches[n_batches - 1][0].shape == (nx, m % mini_batch_size), f"Wrong shape in the last minibatch. {mini_batches[n_batches - 1][0].shape} != {(nx, m % mini_batch_size)}"

```

When testing the mini-batch dataset, I calculated whether m - n_batches * mini_batch_size is greater than 0 when checking whether there is a last batch or not. And, the sample of the last batch size was also calculated like that, and I found that it is cleaner to calculate with modular operation.

# Update parameter with Adam

```python
# GRADED FUNCTION: update_parameters_with_adam

def update_parameters_with_adam(parameters, grads, v, s, t, learning_rate = 0.01,
                                beta1 = 0.9, beta2 = 0.999, epsilon = 1e-8):
    """
    Update parameters using Adam
    
    Arguments:
    parameters -- python dictionary containing your parameters:
                    parameters['W' + str(l)] = Wl
                    parameters['b' + str(l)] = bl
    grads -- python dictionary containing your gradients for each parameters:
                    grads['dW' + str(l)] = dWl
                    grads['db' + str(l)] = dbl
    v -- Adam variable, moving average of the first gradient, python dictionary
    s -- Adam variable, moving average of the squared gradient, python dictionary
    t -- Adam variable, counts the number of taken steps
    learning_rate -- the learning rate, scalar.
    beta1 -- Exponential decay hyperparameter for the first moment estimates
    beta2 -- Exponential decay hyperparameter for the second moment estimates
    epsilon -- hyperparameter preventing division by zero in Adam updates

    Returns:
    parameters -- python dictionary containing your updated parameters
    v -- Adam variable, moving average of the first gradient, python dictionary
    s -- Adam variable, moving average of the squared gradient, python dictionary
    v_corrected = {} # Initializing first moment estimate, python dictionary
    s_corrected = {} # Initializing second moment estimate, python dictionary
    """
    corrected_v={}
    corrected_s={}
    for l in range(1, len(parameters)//2 + 1 ) :

        v['dW' + str(l)] = beta1 * v['dW'+str(l)] + (1-beta1) * grads['dW'+str(l)]
        v['db' + str(l)] = beta1 * v['db' + str(l)] + (1-beta1) * grads['db' + str(l)]
        corrected_v['dW'+str(l)] = v['dW'+str(l)] / (1 - math.pow(beta1 , t))
        corrected_v['db' + str(l)] = v['db' + str(l)] / (1 - math.pow(beta1,t))


        s['dW' + str(l)] = beta2 * s['dW' + str(l)] + (1-beta2) * (grads['dW' +str(l)]) * (grads[' dW' + str(l)])
        s['db' + str(l)] =beta2 * s['db' + str(l)] + (1-beta2) * (grads['db' + str(l)]) * (grads[' db' +str(l)])


        corrected_s['dW'+str(l)] = s['dW' + str(l)] / (1-math.pow(beta2,t))
        corrected_s['db' + str(l)] = s['db' + str(l)] / (1-math.pow(beta2,t))

        parameters['W' + str(l)] -= learning_rate * corrected_v['dW' + str(l)] \
                              /( np.sqrt(corrected_s['dW' + str(l)] ) +epsilon )
        parameters['b' + str(l)] -= learning_rate * corrected_v['db' + str(l)] \
                            /( np.sqrt(corrected_s['db' + str(l)] ) + epsilon )
    return parameters , v, s , corrected_v , corrected_s
```

## Issue1 Adam Scaling term

Now, implement the parameters update with Adam. Recall the general update rule is, for $l = 1, ..., L$:



$$\begin{cases}

v_{dW^{[l]}} = \beta*_1 v_*{dW^{[l]}} + (1 - \beta_1) \frac{\partial \mathcal{J} }{ \partial W^{ [l]} } \\

v^{corrected}*_{dW^{[l]}} = \frac{v_*{dW^{[l]}}}}{1 - (\beta_1)^t} \\

s_{dW^{[l]}} = \beta*_2 s_*{dW^{[l]}} + (1 - \beta_2) (\frac{\partial \mathcal{J} }{\partial W^ {[l]} })^2 \\

s^{corrected}*_{dW^{[l]}} = \frac{s_*{dW^{[l]}}}{1 - (\beta_2)^t} \\

W^{[l]} = W^{[l]} - \alpha \frac{v^{corrected}*_{dW^{[l]}}}{\sqrt{s^{corrected}_*{ dW^{[l]}}} + \varepsilon}

\end{cases}$$



Adam's formula is as above. When scaling V and S, the exponential of beta1, beta2 is used as the scaling term. I'm at a loss as to whether I should use l instead of t here. There seems to be confusion of concepts because there are other networks where time step is expressed as the number of times it goes through layers. However, here time step means the number of times the gradient is updated.

## Issue2 grad element-wise

Also, when finding S, I need to find the element-wise of grad, but an error occurred when I tried to calculate it with np.exp2(). When calculating element-wise, I don't know if it can be calculated with np.exp2(), but I thought that to obtain element-wise, it was just a multiplication calculation.



## Issue3 Update Parameters

When I first wrote the update expression, I wrote `np.sqrt(corrected_s['db' + str(l)] + epsilon )`, but `np.sqrt(corrected_s['db' + str(l)] ) + I have confirmed that there is no error when writing in epsilon`. I'm not sure if this part actually has a big impact on learning.