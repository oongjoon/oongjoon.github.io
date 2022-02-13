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

![9: Experimental setup for study of the visual cortex of cats (HUBEL;... | Download Scientific Diagram](https://www.researchgate.net/profile/David-Macedo-4/publication/330838896/figure/ fig10/AS:722098146263043@1549173022871/Experimental-setup-for-study-of-the-visual-cortex-of-cats-HUBEL-WIESEL-1959.jpg)

Let's take a brief look at the history of convolution. Wiesel and Hubel conducted an experiment to see how they respond to stimuli by shining light on the retina of cat brain cells. They classified the cells of the visual cortex into complex cells and simple cells, and experimented with visual stimulation in simple cells.

![1: Seminal results of Hubel and Wiesel, figure adopted from (Hubel and Wiesel, 1968), Left) orientation selectivity in V1 cells, the recorded cell shows the most sensitivity to the orientation presented in the middle of the column. The sensitivity of cells to a range of orientations is varied smoothly, as shown by the curve. Right) direction selectivity in V1 cells, the recorded cell has the maximum sensitivity to the stimulus presented in the middle of the column. ](https://www.researchgate.net/profile/Mina-A-Khoei/publication/280736145/figure/fig1/AS:614166909947916@1523440210603/Seminal-results-of-Hubel-and-Wiesel-figure-adopted- from-Hubel-and-Wiesel-1968.png)

As a result, we found that it reacts to edges in a specific direction. What this result means is that simple cells capture simple features, such as edges in a certain direction, while more complex cells, or hypercomplex cells, capture more complex features, such as edges moving to endpoints

Generally speaking, from a biological point of view, visual processing processes with more information from simple cells to hycomplex cells.

# What is Convolution ?

Let's talk about the differences between the previously used multilayer neural network and convolution.

![image](https://user-images.githubusercontent.com/50165842/153734655-d71360f8-74a2-4a75-97ac-42b8b42f8a10.png)

Let's say we feed these cat images as input to a multilayer perceptron. If the size of the cat image is 128 * 128 and the number of units in the next hidden layer is about 300, then about 3 million parameters will be needed. If the size of the cat image is 1000*1000, about 300 million parameters are needed.

![Image convolution with an input image of size 7 × 7 and a filter kernel... | Download Scientific Diagram](https://www.researchgate.net/profile/Chaim-Baskin/publication/318849314/figure/fig1/AS:614287726870532@1523469015098/Image-convolution-with-an-input-image-of-size -7-7-and-a-filter-kernel-of-size-3-3.png)

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

