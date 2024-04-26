---
title : "Convolution Neural Network : Variation"



excerpt: "Convolution Variation"

categories:
  - DLArchitecture
tags:
  - [Machine Learning,Convolution,deep learning ,DLSC4]
# classes : wide
toc: true
toc_sticky: true
---

In previous posts, we covered the basic convolution operation and the residual block that influenced other architectures. Although basic convolution is effective in finding complex features in images, it has some problems. To solve this problem, several variations of convolution have emerged, and let's take a look at them.

## 1 x 1 Conv
It is widely known that 1 x 1 Conv That is the main purpose, but there are many other purposes as well. These ideas are worth knowing because they can be applied not only to convolution but also to other deep learning architectures.
### Network in Network
![iamge](https://1drv.ms/i/c/7e81bbcd99889380/IQMjA-bDqJWjQ42GbatCPisrATXSUqmsfEtUqVqD2r3SUc8?width=512)  

1 x 1 Conv is improvement of  "Network in Network "paper. The author of this paper focuses on the fact that in CNN, a feature map represents a meaningful feature, and various variations that express this feature differently are learned. To explain this specifically, the feature map of convolution applies a kernel to each feature map of several channels of the previous layer and adds them. Each kernel can be viewed as one of the ways to express features. In this paper, this is expressed as a latent concept. In other words, various concepts are learned to express the same feature. In other words, the convolution network is learned under the premise that each kernel represents different concepts. In other words, it is linearly seperable. Since it is not possible to directly determine what the parameters of the neural net mean, it can be called a latent concept. It is argued that it is inefficient to use more channels than the previous layer to consider all latent concepts while performing convolution operations in the next layer.

The NIN paper argues that adding a multilayer perceptron for local patches between convolution layers can result in a more discriminative model for local latent concepts. Multilayer Perceptron is a well-known universal function approximator along with radial basis neural network. Since the distribution of latent concepts is unknown, it is argued that universal function approximation, a methodology for learning arbitrary variables, should be used.
Additionally, two reasons are mentioned for using the multilayer perceptron.
- Multilayer perceptron is an architecture that is compatible with convolutional neural networks during backpropagation.
- Multilayer preceptron is a deep neural network that turns local patches, which are input variables, into meaningful features through deep layers.


![image](https://1drv.ms/i/c/7e81bbcd99889380/IQNU8i5lt8kJS4uLYdFxHi-EAQ3abUCC71Drj-t1VJTYsdY?width=256)  

The convolution operation for the existing local patch can be expressed as a formula as above. Kerenl is applied to each channel in the feature map to find features.

![image](https://1drv.ms/i/c/7e81bbcd99889380/IQM1_tn-jTrzSptEZ-RTkiSeATaLHV14a092c-6xYO_otdU?width=256)
However, in the case of a multilayer perceptron, when finding features, all channels are considered at once and features are extracted through deep layers. In other words, all latent concepts from the previous layer are made into new concepts through the fully connected layer.

I think the point of note in this paper is that in order to consider all latent concepts by considering all channels in the feature map, the number of channels was reduced rather than increased. Although not explicitly mentioned in this paper, it was stated that the existing convnet uses over-complete-set filters and too many filters to calculate one concept, and claimed that this was solved with mlp conv. Also, it is said that this operation can be viewed as a parametric cross channel pooling operation. The cross channel pooling operation is used to reduce channels. Therefore, I think it is reasonable to say that meaningful concepts were calculated by considering all latent concepts by reducing the number of channels rather than increasing it like in existing conv.

In conclusion, the existing convolution computes too much information to consider all the information from the previous layer. The intention behind the mlp convolution formula is to calculate only meaningful information as output and pass it as input to the next convolution layer.
### Network in Network vs Inception
In the paper Going Deeper with Convolution published by Google, the MLP Convolution layer of Network in Network was transformed into a 1 x 1 convolution .  Unlike MLP convolution, this paper limited the input patch to a range of 1x1. If all channels are considered for the 1x1 range, this will result in the same as a 1 x 1 convolution operation. The main idea of this paper is to find correlated units, and those units are said to be concentrated in local regions in the lower layer. Therefore, it was decided that 1 x 1 convolution could be sufficient to find it.
### Inception Network
![image](https://1drv.ms/i/c/7e81bbcd99889380/IQPORDogo6XZTLSpSaFZAxB3AUlAf0SbvShkdhBvmR6rd90?width=660)

Inception Network's paper is written based on the idea of a certain paper. The paper is written by a person named Arora and several researchers, and I will attach a link to it in References. According to this paper, if the deep neural network that obtains the probability distribution of data is very large, deep, and sparse, the correlation of the activation of the last layer It is said that an optimal neural network is created by analyzing statistics and clustering neurons with highly correlated output. And, sparse matrices can be approximated with dense submatrices. As mentioned above, in the lower layer, neurons with highly correlated output are concentrated in the local region. This is said to be clustered using a convolution operation. (The connection of neurons through the convolution operation seems to be expressed as clustering.) Since clusters of neurons may be spread over a larger area, the filter size is limited to 3 x 3 and 5 x 5. Because the pooling operation is important in convolution neural networks, a pooling layer is also used.
This architecture can approximate a sparse matrix, but the computation increases significantly after passing several modules.

![image](https://1drv.ms/i/c/7e81bbcd99889380/IQOT10w8PbbuS7nJQez1hcwcAUbIgM86uE5jXVIUS5_UFu8?width=660)
To solve this, dimension reduction and projection are applied in areas where computation increases significantly. Here, the reason for dimension reduction first is that even though low-dimension embedding has a lot of information, low-dimension embedding contains information in a compressed and dense form, making it difficult for the model to learn it. Therefore, dimension reduction is applied first, and then the convolution operation is applied. By doing this, the amount of computation can be reduced while maintaining approximation of the sparse matrix.
By doing this, Inception Network can stack layers deeply while controlling the amount of computation.

You can easily find out the specific figures of the network or the amount of computation reduction by looking at papers or other blogs.
## Separable Convolution
1 x 1 convolution dramatically reduced the amount of computation, but it was difficult to apply to various hardware. In order to use this in devices such as smartphones, it is necessary to further reduce the amount of computation. Separable Convolution is that idea.

### Original Conv vs Seperable Conv
![image](https://1drv.ms/i/c/7e81bbcd99889380/IQPh-iZ-ZAXmQ5gOEJGzP9ZBAZWhYQx2Axyhl--pNCAW3Rs?width=660)
Unlike original convolution, which creates a feature map by combining all input channels, seprable convolution creates a feature map with one channel. Separable convolution is very computationally efficient compared to original convolution. However, combining them does not create new features. Therefore, a method for combining feature maps is needed. In MobileNet, new features are created by combining each channel of the feature map through 1 x 1 convolution.
## BottleNeck Block
![image](https://1drv.ms/i/c/7e81bbcd99889380/IQMSOXEfK6_3TLmkUO3cS2dCASNUaVJzwzBsv3YV2_XCP4Q?width=1024)
Using 1 x 1 convolution and separable convolution, the MobileNetV2 paper introduces a new convolution architecture called bottleneck block. From a computation perspective, it appears that the channel was increased and then decreased, but there is a hidden mathematical reason behind this. The change in dimension is an idea that applies to other architectures as well, so knowing it will help you understand similar ideas.
### low-dimension embedding
![image](https://1drv.ms/i/c/7e81bbcd99889380/IQPb9H7oIxoJTJje1VgQB1ZcAZ9VfutmlV-Wyff-mYf1iCA?width=1024)

In Convolution Neural Network, activation tensors have the form height * width * dimension. These tensors form a “manifold of interest”. It has long been known that the “manifold of interest” of neural networks can be embedded in low-dimension. In other words, information encoded in a feature map with a certain channel is located in a certain manifold, and this manifold is a subspace with a lower dimension than the dimension of the feature map.

However, this fact is not covered by the non-linear activation function.
A manifold can exist in low-dimension if special conditions are met.

![image](https://1drv.ms/i/c/7e81bbcd99889380/IQNVdShjBRHrQYBj9tu78966AYumbZAEH7c0HwCTvdQkhzE?width=1024)
After applying transformation B to input x, if the output of ReLU(Bx) transformation with ReLU activation has a non-zero volume S, the output is still limited to linear transformation. The figure above shows that the larger the dimension, the better the 2-D projection preserves information. When applying ReLU, information loss is inevitable. However, if the input channel is large enough, information will be preserved even if the number of channels is small.

### MobilenetV2
MobileNetV2 used ReLU6 as the activation function. This is a ReLU function that limits the range of the max value to 6. This is because on-device uses a low-precision model, and the max value is limited to 6, so overflow does not occur in low-precision and operates robustly.

## EfficientNet
EfficentNet is about the tradeoff between performance and speed of the entire ConvNet rather than the transformation point of the convolution. It would be good to know because it is an idea that can be used in other deep learning architectures.
EfficientNet adjusts the trade-off relationship between network performance and speed through width, resolution, and depth. As width, resolution, and depth increase, network performance improves, but speed decreases. Therefore, we argue that it is good to consider the given computation resources and performance requirements and scale according to the situation.
## Summary
Today we learned the following:
- Network in Network allows the model to learn complex functions by increasing non-linearty without learning parameters that detect unnecessary features.
- Inception Network uses NIN by transforming it into 1 x 1 Conv and considers all representations of unnecessary features at once.
- Separable Convolution shows a significant reduction compared to Origianl Convolution in terms of computation.
- The BottleNeck block assumes that a manifold that can be expressed as low-embedding exists in a high-dimensional feature.
- Network performance and speed are a tradeoff relationship. This can be adjusted by depth, length, and width.
## References

[Network In Network](https://arxiv.org/abs/1312.4400)  

[DLS Conv](https://www.coursera.org/learn/convolutional-neural-networks/home/week/2)

[MobileNetV2](https://arxiv.org/abs/1801.04381)

[Sparse deep network](https://arxiv.org/pdf/1310.6343.pdf)