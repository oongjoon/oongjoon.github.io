---
title : "Transfer Learning,Multitask learning, end-to-end Learning"

excerpt: "ML Project structing "

categories:
  - DLS_C3
tags:
  - [Machine Learning,Coursera,deep learning ,DLSC3,ML Strategy]
# classes : wide
toc: true
toc_sticky: true
---
When learning a NeuralNetwork, if you are given as much data as you want, there is no need to devise a strategy for learning the NeuralNetwork. However, in the real world, sufficient data is rarely provided. Therefore, knowing study strategies depending on the situation will be of great help.

## Transfer Learning
![image](https://1drv.ms/i/c/7e81bbcd99889380/IQPzJrVtcL4jT5_Hh47sGhFfAScwCAmYd0tmRZC4tKauyos?width=588&height=278)  


Transfer Learning is a method of training a NeuralNetwork that performs Task B by using the learned NeuralNetwork to learn a NeuralNetwork that performs Task A. At this time, learning Task A is called Pre-training, and learning Task B is called Fine-tuning.

Examples of Transfer Learning include:

* image recognition -> radiology diagnosis
* speech recognition -> wakeword/keyword detection  
  
![image](https://1drv.ms/i/c/7e81bbcd99889380/IQPEmziav-dfRJRUx_7zY2ZdAan91LOB6qjkohYtJmVak9U?width=416&height=195)

In the case of transfer learning, it can be applied when the data in Task A is greater than the data in Task B. This means that Task A's data is relatively more valuable than Task B's data.
### when to apply transfer learning
In order to apply transfer learning, the following conditions must apply.
* The inputs of Task A and B are the same (same format).
* The number of data in Task A is greater than the number of data in Task B.
* The low-level features learned in Task A are helpful in learning Task B.  


## Multitask Learning
MultiTask Learning is a method of training multiple tasks **simultaneously** to improve performance by taking advantage of the similarities and differences between different tasks.
Examples of MulitTask Learning include:

* multi-label classification
   * Usually, classification uses one label, but multi-label refers to a task in which multiple labels are mapped to one image. Therefore, when calculating the loss function, the loss for each label is added and parameter updates are performed.
* object detection
   * Object Detection is the most famous multi-task learning. Object detection simultaneously outputs object existence, bounding box detection, and label classification. Likewise, when calculating Loss, this is added to update the parameter.

One of the characteristics of multi-task learning is that learning is possible even if some errors occur in the labels. ***Only the loss of labeled y is calculated and updated.*** For example, in the case of object detection, if the exist value is 0, the remaining labels are not labeled. However, when learning, the parameter is updated by calculating the loss using only the existence probability, excluding the bounding box and label classifiaction.

### when to apply multitask learning
Multi-task learning can be considered in the following situations:

* It is helpful when training when multiple tasks share low-level features.
* **Generally**, the data size of each task is similar.
   * Multi-task Learning is equivalent to fine-tuning one task after pretraining with a task using 99,000 data if 100 tasks each have 1,000 pieces of data. If the data of one task is significantly large, transfer learning will not work well because the number of data in the fine-tuning task is greater than that in the pre-training task. (Transfer learning is a superordinate concept of multitask learning).
* Train a sufficiently large Neural Network.
   * If the Neural Network is not large enough, a Neural Network that performs each task individually will have better performance.
## End-to-End
End-to-End refers to a Neural Network that derives output directly from data. When solving a problem in the field of Computer Science, the problems are broken down into small units, small problems are solved, and these are combined to solve the original problem. Neural Network can also solve the original problem using Neural Network or Domain Knowledge, which solves each problem by dividing the problem into small units.
Examples include:
* Speech Recognition
   * End-to-End: audio -> transcript
   * middle: audio -> phonemes -> ...-> transcript
   * subproblem: audio -> phonemes -> words ->...-> transcript
* Face Recognition
   * End-to-End: Image -> Face Identity
   * subproblem: Image -> Face Detection -> Face Identity
* Machine translation
   * End-to-End: English -> French
   * subproblem:English -> Text Analysis -> ... -> French

### Pros and Cons  
When applying End-to-End, there are the following advantages and disadvantages.
*Pros
   * Problem solving using only data: If you provide data as input, you can obtain the desired output.
   * Solving less subproblems: It can be said that relatively less human effort is required because it requires less process of decomposing the problem into smaller problems and solving them.
*Cons
   * Requires a lot of data: Machine Learnig requires more data as the problem becomes more complex. When dividing an existing problem into SubProblems, each subproblem requires relatively less data, so the more subproblems are divided, the more likely it is that less data will be needed. If you create a Neural Network without dividing it into subproblems, more data will be needed.
   * Increases the likelihood of skipping useful subproblems: This may not always be correct, but it is generally correct. For example, in the case of Speech Recognition, after solving the subproblem of detecting a phoneme, you solve the subproblem of converting it to a transcript. This solves the existing problem. However, this is the best method from the current perspective, and it cannot always be said to be right because a better method may be discovered in the future.

### Key points in end-to-end

When applying end-to-end, the key point is to always consider whether there is enough data to learn the complexity of the X->Y mapping function.
For example, in the case of autonomous driving, a lot of data is needed to control a car with only sensors such as image, radar, and lidar.
Therefore, reduce the number of data required by dividing it into the following steps: input -> Object Detection -> Path Setting -> Manipulation. DeepLearning algorithms will be used for object detection, motion planning for path setting, and control dynamics for manipulation.