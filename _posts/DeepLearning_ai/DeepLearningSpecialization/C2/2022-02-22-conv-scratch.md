---
title : "Convolution Assignment from scratch"



excerpt: "DeepLearning.ai Dls Convolution "

categories:
  - DLS_C2
tags:
  - [Machine Learning,Coursera,deep learning ,C4]
# classes : wide
toc: true
toc_sticky: true
---
I solved the problem of convolution operation in DeepLearning Specialization with only input and output docstrings without hints. Convolution operatoin is supported by most deeplearning frameworks, but I felt a lot of points while implementing it myself.



# Convolution forward

```python
# GRADED FUNCTION: conv_forward

def conv_forward(A_prev, W, b, hparameters):
    """
    Implements the forward propagation for a convolution function
    
    Arguments:
    A_prev -- output activations of the previous layer, 
        numpy array of shape (m, n_H_prev, n_W_prev, n_C_prev)
    W -- Weights, numpy array of shape (f, f, n_C_prev, n_C)
    b -- Biases, numpy array of shape (1, 1, 1, n_C)
    hparameters -- python dictionary containing "stride" and "pad"
        
    Returns:
    Z -- conv output, numpy array of shape (m, n_H, n_W, n_C)
    cache -- cache of values needed for the conv_backward() function
    """
    # unpacking을 잘 이용못햇음
    n_H_prev = int(A_prev.shape[1])
    n_W_prev = int(A_prev.shape[2])
    n_C_prev = int(A_prev.shape[3])
    m = int(A_prev.shape[0])
    n_C =int(W.shape[-1])
    f = int(W.shape[0])
    p = hparameters['pad']
    s= hparameters['stride']
    A_pad  = zero_pad(A_prev , p)
    n_H  = int(np.floor((n_H_prev+2 * p - f) / s + 1 ))
    n_W = int(np.floor((n_W_prev +2 *p -f) /s + 1))
    Z  = np.zeros((m,n_H,n_W,n_C))

    # for문을 생각해낼 떄 반복적인 부분의 pattern을 조사해서 묶지 않앗음
    # 반복적인 부분의 pattern을 도출해내면 이 제 이 일반식을 적용하는 단계를 생각하지 않고 ( vert_start, vert_end,hori_start,hori_end 를 계산안함
        # ) 무의식적으로 바로 계산 (h*s, h*s + f .. 바로 도출)
    for i in range(m):
        for c in range(n_C):
            for h in range(n_H) :
                for w in range(n_W) :
                        Z[i,h,w,c] = conv_single_step(A_pad[i,h*s : h*s +f , w *s : w*s + f ,:].reshape((f,f,-1)) 
                            ,W[:,:,:,c].reshape(f,f,n_C_prev) , b[:,:,:,c].reshape(1,1,1))
    cache=(A_prev,W,b,hparameters)
    return Z ,cache
```

This is a task to implement the forward operation of one convolution layer from scratch. I got the correct answer, but I felt that the process of deriving the correct answer was not logical.

The forward operation of the convolution layer repeats the convolution operation to make one output pixel countless times. When looking at one channel of input and filter, if a hyperparameter called stride is given, the filter is moved by this interval and convolution operation is performed to create one output pixel. That is, since there is a repetitive pattern that produces output while moving the interval as much as stride, an iterative algorithm is used using the for statement. Since there is a repetitive pattern, you need to generalize the filter position. I did not find a repetitive pattern, but generalized the process of repeating m * n_C * n_H * n_W at once. Since I knew roughly what the outcome would be, I think it was the result of a leap in the logical step of thinking.

The logical process in my opinion is this.

1. Consider one input and one filter.
2. The filter executes convolution operation while moving vertically and horizontally.
3. We repeat the operation vertically and horizontally, but let's consider the operation in the horizontal direction with the vertical direction fixed.
4. The spacing in the horizontal direction is a stride. Therefore, hori_start = ws , hori_end = ws +f . Since there is a repetitive pattern, the algorithm was constructed iteratively using the for statement.
5. The vertical spacing is also a stride. Therefore, vert_start = hs and vert_end = vs + f. Since there is a repetitive pattern, the algorithm is iteratively constructed using the for statement.
6. Forward propagation of the convolution layer for one input and filter can be defined through the process of 4,5.
7. This is repeated as many as n_C channels, and repeated for m batches. It proceeds forward propagatoin for n_C pieces and repeats this for m batches.

I skipped all the logical processes and only derived the results. I thought I had to use a for expression, so I used a for statement, so I just coded it. There was no process of thinking about the problem and translating it, so I couldn't say I knew it completely.

Also, apart from convolution, I couldn't use python's unpacking function well. Unpacking the shape make coding easier, but multiple lines were used more inefficiently. Ideally, it would be possible to fix it like this:



```python
# GRADED FUNCTION: conv_forward

def conv_forward(A_prev, W, b, hparameters):
    """
    Implements the forward propagation for a convolution function
    
    Arguments:
    A_prev -- output activations of the previous layer, 
        numpy array of shape (m, n_H_prev, n_W_prev, n_C_prev)
    W -- Weights, numpy array of shape (f, f, n_C_prev, n_C)
    b -- Biases, numpy array of shape (1, 1, 1, n_C)
    hparameters -- python dictionary containing "stride" and "pad"
        
    Returns:
    Z -- conv output, numpy array of shape (m, n_H, n_W, n_C)
    cache -- cache of values needed for the conv_backward() function
    """
    # unpacking을 잘 이용못햇음
    n_H_prev , n_W_prev , n_C_prev ,m =  A_prev.shape
    f , n_C = W.shape
    p = hparameters['pad']
    s= hparameters['stride']
    A_pad  = zero_pad(A_prev , p)
    n_H  = int(np.floor((n_H_prev+2 * p - f) / s + 1 ))
    n_W = int(np.floor((n_W_prev +2 *p -f) /s + 1))
    Z  = np.zeros((m,n_H,n_W,n_C))

    # for문을 생각해낼 떄 반복적인 부분의 pattern을 조사해서 묶지 않앗음
    # 반복적인 부분의 pattern을 도출해내면 이 제 이 일반식을 적용하는 단계를 생각하지 않고 ( vert_start, vert_end,hori_start,hori_end 를 계산안함
        # ) 무의식적으로 바로 계산 (h*s, h*s + f .. 바로 도출)
    for i in range(m):
        for c in range(n_C):
            
            for h in range(n_H) :
            	vert_start = h * s
                vert_end= h*s +f
            	for w in range(n_W) :
                    	hori_start = w *s
                        hori_end = w*s +f
                        Z[i,h,w,c] = conv_single_step(A_pad[i,vert_start : vert_end , hori_start : hori_end ,:].reshape((f,f,-1)) 
                            ,W[:,:,:,c].reshape(f,f,n_C_prev) , b[:,:,:,c].reshape(1,1,1))
    cache=(A_prev,W,b,hparameters)
    return Z ,cache
```





# Conv Pooling Forward

The pooling layer has basically the same format as the conv layer. However, one of the different ideas used here is that the pooling operation is treated as a function object. Considering it simply, the pooling operation must be used differently depending on the mode, so the code that controls the flow using the if statement had to be controlled within the for statement. However, since this itself is an operation of the layer, the pooling operation is returned as a function object, and in the for statement, the pooling operation is performed regardless of whether it is max or average.

```python
def pool_forward(A_prev, hparameters, mode = "max"):
    """
    Implements the forward pass of the pooling layer
    
    Arguments:
    A_prev -- Input data, numpy array of shape (m, n_H_prev, n_W_prev, n_C_prev)
    hparameters -- python dictionary containing "f" and "stride"
    mode -- the pooling mode you would like to use, defined as a string ("max" or "average")
    
    Returns:
    A -- output of the pool layer, a numpy array of shape (m, n_H, n_W, n_C)
    cache -- cache used in the backward pass of the pooling layer, contains the input and hparameters 
    """
    def cus_pool(mode):
        if mode =="max" :
            return np.amax
        else :
            return np.ma.average
    pooling = cus_pool(mode)
    # print(pooling)
    (m , n_H_prev, n_W_prev , n_C_prev) = A_prev.shape
    s , f  = [ v for k,v in hparameters.items()]
    n_H = int(np.floor((n_H_prev-f) /s + 1 ))
    n_W  = int(np.floor( (n_W_prev - f) /s + 1 ))
    n_C = n_C_prev
    A = np.zeros((m,n_H,n_W,n_C))
    for i in range(m):
        for c in range(n_C) :
            for h in range(n_H) :
                vert_start = h*s 
                vert_end = h*s + f
                for w in range(n_W) :
                    hori_start = w *s 
                    hori_end = w*s + f
                    A[i,h,w,c] = pooling(A_prev[i,vert_start:vert_end, hori_start:hori_end , c])

    # print(f'f : {f}  s L {stride}')
    # cache = []
    cache = (A_prev , hparameters)
    return A, cache
```









# Conv_Backward

Convolution backward seems to have been the part that I was worried about for the longest time. It seems that the main cause was that the back propagation was only memorized in a scalar form between single node graphs or only the official result. I did not know how to generalize this and derive it in matrix form.

Some people try to derive the transformed coordinates from the matrix when they show the linear transformation in the linear algebra, but it can be said that they do not understand the linear algebra at all. After defining an operation called linear transform , one of its representations is just matrix multiplication.

I think that back propagation also represents the multiplication operation of the computation graph as a matrix. Back propagation is the transfer of partial derivative values from the computation graph to the connected nodes.


$$
\Sigma \Sigma \Sigma F[m,c,w,h] * A[m,c,w' , h'] = Z[m,c,W,H]
$$


If F is the kernel, A is the input, and Z is the output, the process of calculating one pixel is as above.

Since convolution is not a fully connected layer, the method using matrix transpose is not applied. From a computational graph point of view, several input pixels are connected to one output pixel. Just like convolution operation, one pixel value, i.e., derivative with respect to Wegiht, bias, and activation for a scalar, is obtained and accumulation is carried out.

To explain the formula, the pixel at the h, w position in channel c of the mth training sample has a connection with the pixel of Z[m,hori_start:hori_end,vert_start:vert_end,c] with numpy syntax.

```python
def conv_backward(dZ, cache):
    """
    Implement the backward propagation for a convolution function
    
    Arguments:
    dZ -- gradient of the cost with respect to the output of the conv layer (Z), numpy array of shape (m, n_H, n_W, n_C)
    cache -- cache of values needed for the conv_backward(), output of conv_forward()
    
    Returns:
    dA_prev -- gradient of the cost with respect to the input of the conv layer (A_prev),
               numpy array of shape (m, n_H_prev, n_W_prev, n_C_prev)
    dW -- gradient of the cost with respect to the weights of the conv layer (W)
          numpy array of shape (f, f, n_C_prev, n_C)
    db -- gradient of the cost with respect to the biases of the conv layer (b)
          numpy array of shape (1, 1, 1, n_C)
    """   
    A_prev, W,b,hparameters = cache
    m , n_H_prev , n_W_prev, n_C_prev= A_prev.shape
    _,n_H,n_W,n_C = dZ.shape
    s , p  = hparameters['stride'] , hparameters['pad']
    f,_,_,_ = W.shape
    dA_prev_padded = np.zeros((m,n_H_prev+2*p , n_W_prev + 2*p , n_C_prev))
    dW = np.zeros((f,f,n_C_prev, n_C))
    db = np.zeros((1,1,1,n_C))
    A_prev_padded  = zero_pad(A_prev,p)
    for i in range(m):
        for c in range(n_C) :
            for h in range(n_H) :
                vert_start = h * s 
                vert_end = h*s +f
                for w in range(n_W) :
                    hori_start = w *s 
                    hori_end = w*s + f
                
                    dA_prev_padded[i,vert_start : vert_end  ,hori_start : hori_end ,: ] += dZ[i,h,w,c] * W[:,:,:,c]
                    dW[:,:,:,c] +=  dZ[i,h,w,c] * A_prev_padded[i,vert_start:vert_end , hori_start:hori_end, :]
                    db[:,:,:,c] += dZ[i,h,w,c]
    dA_prev = dA_prev_padded[:,p:-p , p:-p,: ]
    assert(dA_prev.shape == (m, n_H_prev, n_W_prev, n_C_prev))
    return dA_prev, dW , db
```

# Conv_Pooling_Backward

Backward propagation of the Pooling Layer is different from the Convolution Layer.

max operation is an operation that preserves only one node among multiple nodes. That is, it can be seen that only one node is connected to an edge in the computation graph. In other words, the derivative is passed only to this node.

average operation is the mean value of multiple nodes. That is, it is an operation that accumulates activations and then divides them by the number of activations, that is, the number of parameters in the filter. You need to reflect this fact when doing backward propagation.

By taking advantage of the matrix operation of the max operation, we created a mask matrix and used it. After finding the position of the max node, it seems okay to update only that position, but since the mask operation itself is a constant time of O(1), I thought it was a negligible overhead, and I thought that the mask operation was good for computational convenience. I heard.

```python

def create_mask_from_window(x):
    """
    Creates a mask from an input matrix x, to identify the max entry of x.
    
    Arguments:
    x -- Array of shape (f, f)
    
    Returns:
    mask -
    """
    mask = (x == np.max(x))    
    return mask

def distribute_value(dz, shape):
    """
    Distributes the input value in the matrix of dimension shape
    
    Arguments:
    dz -- input scalar
    shape -- the shape (n_H, n_W) of the output matrix for which we want to distribute the value of dz
    
    Returns:
    a -- Array of size (n_H, n_W) for which we distributed the value of dz
    """    
    n_H, n_W  = shape
    a = np.ones((n_H,n_W))
    a[:,:] = dz / (n_H*n_W)
    return a




def pool_backward(dA, cache, mode = "max"):
    """
    Implements the backward pass of the pooling layer
    
    Arguments:
    dA -- gradient of cost with respect to the output of the pooling layer, same shape as A
    cache -- cache output from the forward pass of the pooling layer, contains the layer's input and hparameters 
    mode -- the pooling mode you would like to use, defined as a string ("max" or "average")
    
    Returns:
    dA_prev -- gradient of cost with respect to the input of the pooling layer, same shape as A_prev
    """

    
    m , n_H,n_W,n_C = dA.shape

    A_prev , hparameters = cache
    s , f  = hparameters['stride'] , hparameters['f']
    dA_prev = np.zeros(A_prev.shape)
    for i in range(m):
        for c in range(n_C) :
            for h in range(n_H) :
                vert_start = h * s 
                vert_end = h*s + f
                for w in range(n_W) :
                        hori_start  = w * s
                        hori_end = w*s + f
                        A_slice = A_prev[i,vert_start:vert_end,hori_start:hori_end,c]

                        ## computational graph 관점으로 생각해서 식적기
                        ## chain rule 관점에서 생각해보기 
                        if mode == "max" :
                            masking = create_mask_from_window(A_slice)
                            dA_prev[i,vert_start:vert_end,hori_start:hori_end,c] += dA[i,h,w,c]  * masking
                        elif mode=="average" :
                            masking = distribute_value(dA[i,h,w,c],(f,f))
                            dA_prev[i,vert_start:vert_end,hori_start:hori_end,c] +=  masking
                        
    return dA_prev
```

