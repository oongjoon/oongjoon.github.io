---
title : "Convolution Neural Network : intuition"



excerpt: "Convolution Intuition"

categories:
  - DLArchitecture
tags:
  - [Machine Learning,Convolution,deep learning ]
# classes : wide
toc: true
toc_sticky: true
---

One of the fields that has made a lot of progress due to deep learning is computer vision. The field of computer vision has made various developments as it began to graft deep learning. When you think of deep learning in computer vision, you think of Convolution Neural Networks. Convolution Neural Networks have already been implemented in various deep learning frameworks.You can use a convolutoin neural network end-to-end without knowledge of convolution, but you need to know about intution of convolution in order to interpret the results or perform experiments with transformations. I think we should  know what the Convolution Neural Network is trying to replace in human logic programming and  what intution it contains.

# History of Visual Processing

![Convolutional Networks II](data:image/jpeg;base64,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)

Let's take a brief look at the history of convolution. Wiesel and Hubel conducted an experiment to see how they respond to stimuli by shining light on the retina of cat brain cells. They classified the cells of the visual cortex into complex cells and simple cells, and experimented with visual stimulation in simple cells.

![1: Seminal results of Hubel and Wiesel, figure adopted from (Hubel and Wiesel, 1968), Left) orientation selectivity in V1 cells, the recorded cell shows the most sensitivity to the orientation presented in the middle of the column. The sensitivity of cells to a range of orientations is varied smoothly, as shown by the curve. Right) direction selectivity in V1 cells, the recorded cell has the maximum sensitivity to the stimulus presented in the middle of the column. ](https://www.researchgate.net/profile/Mina-A-Khoei/publication/280736145/figure/fig1/AS:614166909947916@1523440210603/Seminal-results-of-Hubel-and-Wiesel-figure-adopted- from-Hubel-and-Wiesel-1968.png)

As a result, we found that it reacts to edges in a specific direction. What this result means is that simple cells capture simple features, such as edges in a certain direction, while more complex cells, or hypercomplex cells, capture more complex features, such as edges moving to endpoints

Generally speaking, from a biological point of view, visual processing processes with more information from simple cells to hycomplex cells.

# What is Convolution ?

Let's talk about the differences between the previously used multilayer neural network and convolution.

![image](https://user-images.githubusercontent.com/50165842/153734655-d71360f8-74a2-4a75-97ac-42b8b42f8a10.png)

Let's say we feed these cat images as input to a multilayer perceptron. If the size of the cat image is 128 * 128 and the number of units in the next hidden layer is about 300, then about 3 million parameters will be needed. If the size of the cat image is 1000*1000, about 300 million parameters are needed.

![In a convolutional neural network (CNN), when convolving the image, is the  operation used the dot product or the sum of element-wise multiplication? -  Cross Validated](https://i.stack.imgur.com/MkFSC.png)

A convolution neural network uses a filter to element-wise (or dot product) an input image area equal to the filter and filter size, and uses the calculated result as the pixel of the output image. Unlike MLP, relatively few parameters are used because only as many parameters as the filter are used.

# What is filter?

## Filter Example (vertical edge)

![image](https://user-images.githubusercontent.com/50165842/153734814-d8802fb5-c305-4401-b4f8-968433034dac.png)

(The photo above was taken from Professor Andrew's lecture.)



Given an image, the position of the object on the image is determined according to the difference in pixel values. Here, if you need to classify vertical edges, you can use the 3*3 filter given in the photo. If it goes through the process of calculating the next output pixel by sliding that filter, the output pixel is maintained at 0 where the pixel value is high, and when the boundary is reached, the output pixel value increases, and when the pixel value goes to a low pixel value, the pixel value again You can see that this stays at 0.

If the pixel value is high, it is displayed brightly, and if it is low, it is expressed darkly on the computer screen. Therefore, the original image shows the left side bright and the right side dark. The filter is shown in the order of white, gray, and black, and the output is shown with a thick center. The reason the output image looks thick is because the size of this image is small as 6 * 6 . A , 1000 * 1000 will come out well without looking thick.

## Another Filter

![image](https://user-images.githubusercontent.com/50165842/153736235-911a12c9-d147-454a-98a8-9203f2b02b3d.png)

(Picture taken from Professor Andrew's lecture.)



The vertical edge detector above detects the edge by sliding the filter from the bright pixel to the dark pixel. However, when sliding from the dark pixel to the bright pixel, the output activation becomes gray pixels on both sides and black pixels in the middle. 

If it is not important to go from dark to bright pixels and from bright pixels to dark pixels here, you can use absolute values to only detect vertical edges.

![image](https://user-images.githubusercontent.com/50165842/153736518-a8d9f971-9e6c-4b80-9682-83dacd9ba2c6.png)

There are various filters other than those that detect vertical edges. A horizontal filter that contrasts with vertical is one example.

The horizontal filter is done by rotating the vetrical filter. As with the vertical filter above, when the boundary is reached, the output pixel value increases and the horizontal line becomes visible. In the example above, the horizontal filter is sliding on the check pattern. Unlike the existing vertical edge, you can see that there is a part that falls from 30 to 10. This indicates that there is a positive pixel on the left and a negative pixel on the right. Because when you mix two values, you get an intermediate value.

As with the vertical edge detector, it is a 6*6 size image, so the transition area is conspicuous. If it is a 1000 * 1000 image, the transition area will not be noticeable.



## Learning Edge detector

![image](https://user-images.githubusercontent.com/50165842/153736894-172992d5-e0dc-4b5e-be84-ec46ed7b41ce.png)

In addition to this, you can also create filters such as 45 degree and 70 degree lines. A 45 degree flter can be calculated, but a special filter like 70 degree is difficult to find. And, there are things called sobel filter and scharr filter made by computer vision scientists. Previously, scientists found these filter values through experiments, but deep learning does not let vision scientists select them, but  learns through backpropagation.

. Therefore, the convolutional neural network is to learn these filters by setting the convolution filter as a learnable parameter. In other words, it allows the neural net to learn low-level features such as edges.

# Convolution Layer

Above, we investigated the motivation for several convolution layers, such as what visual processing and convolution filters are, and what parts they replace humans in deep learning. Then, to use a convolution layer in a neural net, we need to define a forward operation, so let's see what to consider when defining it.

## Convolution forward

![image](https://user-images.githubusercontent.com/50165842/153737095-2755523b-05c6-4bf0-9303-faafbfd4d72d.png)

(I brought the image of the Standford cs231n lecture 5 pdf.)

First, let's learn what the convolution operation is. Convolution is to dot-product filter and image pixel and use it as output pixel of actvation map. You can see that the output image size is as small as 28. The image size follows the formula (N-F+1) * (N-F+1). (N: the length of one side of the image, F: the length of one side of the filter)

 Dot-product should not be performed on the range that exceeds the input size, so it reflects the number of pixels minus the filter size from the total input size. We give it +1 for calculations as we need to reflect both ends of the interval.

## Padding

![image](https://user-images.githubusercontent.com/50165842/153737599-333b0cf7-17f7-4a9a-8179-f7e3022953bc.png)

There are several disadvantages of convolution operation.

1. The size of the image gets smaller every time it goes through each layer.
2. Pixels on the corner or edge side are reflected only once in the output.

To solve these shortcomings, the idea of padding has been proposed.



Some pixels are attached to the outside of the image. Usually, zero padding is used, which is called zero-padding. If you ask, is it possible to padding with a different value instead of zero padding, the answer is yes. However, in general, it is known that 0 works well, so 0 is used.

Here, if padding is performed, the image size becomes (N-F+1+2p) * (N-F+1+2p).

## Stride

Up to now, the filter has been moved only by one space, but it is not necessary to move the filter by one space only. Stride determines how many spaces to move the filter.

Considering this stride, the size of the output is $$ (\lfloor {(N+2p-f) \over S} \rfloor + 1 )(\lfloor {(N+2p-f) \over S }\rfloor + 1)$$.

To put this into words, there is an element at the (0,0) position of the filter and an input pixel that does dot-product. We do +1 because we need to include both ends of the interval as described earlier. (It is convenient to think of the <= range in the vertical line.)

If the stirde value is set to 1 or more, there may be parts that are not reflected. In some case, the output image is asymmetric because it does not reflect all input pixels symmetrically. It is said that the stride value in these cases is rarely used.

## Convolution over Volume

![image](https://user-images.githubusercontent.com/50165842/153737095-2755523b-05c6-4bf0-9303-faafbfd4d72d.png)

In the previous explanation, I assumed that the input image was 2d and explained it. However, the images that actually exist are mostly rgb images, i.e. 3-d images.

![image](https://user-images.githubusercontent.com/50165842/153738165-66ef0891-cae4-47b9-8ff3-434c10a7f841.png)



If you look in more detail, there are several 2d images and there are several 2d filters. This new dimension is called depth or channel. The operation is performed like the convolution operation we saw before, and the convolution operation is performed on the image and filter of the same depth.

In the above example, it can be seen that the vertical edge is detected only in the red channel.

![image](https://user-images.githubusercontent.com/50165842/153738289-1fdb7362-877c-4640-bb16-7df2aafa9f01.png)

The output of the convolution operation so far has been a 2-d image. However, you can create a 3-d output image by using different filters of the same shape. At this time, it must be remembered that the output of each channel is different because it goes through a different filter.

## Putting it together

After putting them all together, it's important to know exactly how many parameters and hyperparameters you need.

![image](https://user-images.githubusercontent.com/50165842/153738536-f038eeab-4646-4698-9c2e-a0aaae5f06f6.png)

In this example, when going from the first layer to the second layer, the number of parameters will be (5 * 5 * 3 + 1) * 6 . Here +1 means to add bias.

Let's define the general lth conv layer.

- f[l] = filter size, p[l] = padding size , s[l] = stride size
- Input : n_h[l-1] * n_w[l-1] * n_c[l-1]
- output : n_h[l] * n_w[l] * n_c[l]
- activation_size : n_h[l] * n_w[l] * n_c[l]
- filter size: f[l] * f[l] * n_c[l-1] * n_c[l]
- bias : 1 * 1 * 1* n_c[l]
- n_h,w[l] = $$ (\lfloor {(n_h,w[l-1+2p[l]-f[l]) \over s[l]} \rfloor + 1 ) $$
- A[l] : m * n_h[l] * n_w[l] * n_c[l]

A[l] means the output result for m mini batches. Professor Andrew said that it is convenient if the implementation of the bias is in 4 dimensions, but personally I think that it means that the position is accessed in the same dimension as the weight, so it is easy to understand in terms of logic. If you look at the cs231n assignment, it was processed in one dimension, but it is thought to be a difference in coding style.



# Other Layers

In a Convolution Neural Net, only Conv layers do not exist. There are other layers as well. Let's take a look at one of them, the pooling layer.

## Max Pooling Layer

![image](https://user-images.githubusercontent.com/50165842/153739642-d4a195a7-e574-4c44-b800-2195dae22362.png)

The max pooling layer is an operation that selects the maximum value from the region while stride the filter. In 1box (blue), the pixel value is large, which means that various features have been found (ex. cat's eyes, nose, mouth) . 2 boxes (light blue) have relatively small pixel values, which means that no features were found.

Usually, max pooling is often described as reducing image size, but there is a hidden intuition here. The role of max pooling is to ensure that as many features are found as possible. As the filter strides, if the pixel value is high, that is, if there is a feature, it is preserved with the max operation. If the feature is not found, the value of max operation will be small.

In other words, we downsampling the image, and the features are found and preserved as much as possible with the max operation, so that a similar effect can be achieved with a small image.

Listening to Standford lectures, there are students who ask questions about the difference between pooling and stride. There, Professor Serena answers (as of 2017) that there are quite a few architectures that replace the latest architectures with stride instead of pooling. However, you add an additional comment that pooling gives better results in practice. Considering the intuition of pooling, I personally think that it cannot be replaced as it is an operation that plays a completely different role from stride convolution.

In addition to this, average pooling also exists, and it is said that max pooling is mainly used.

As you can see from the picture, this pooling operation is performed independently of each channel. That is, it proceeds for 2-d input.

The important thing here is that pooling has no parameters. Because the operation of pooling requires max and average functions, there is no need for learnable parameters.

 # Why Convolution?

At the beginning of the post, it was said that convolution can reduce parameters than MLP. Let's take a closer look at why reducing parameters and other reasons to use convolution.

## Parameter sharing

Convolution uses the same parameters for all pixels to create an activation map. Since the same parameters are used, the same feature can be found in different positions of the image. For example, when detecting vertical edges with a 3 x 3 filter, no other parameters are needed to detect the vertical edges on the left and right sides of the image. In other words, with fewer parameters than the multilayer perceptron. The same features can be extracted.

## Sparse Connections

Unlike the Fully Connected Layer, the activation map is not connected to all pixels of the input of each pixel. Therefore, even if the image is slightly damaged, even if it is slightly shifted, the overall feature extraction is not significantly affected. It is said to capture translation invariance well.

# Reference

[Lecture 5 | Convolutional Neural Networks - YouTube](https://www.youtube.com/watch?v=bNb2fEVKeEo&t=3164s)

[Convolutions Over Volume | Coursera](https://www.coursera.org/learn/convolutional-neural-networks/lecture/ctQZz/convolutions-over-volume)

