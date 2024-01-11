---
title : "Train,Dev,Test의 error를 어떻게 해석해야 할까요?"

excerpt: "ML Project structing "

categories:
  - DLS_C3 
tags:
  - [Machine Learning,Coursera,deep learning ]
# classes : wide
toc: true
toc_sticky: true
---
I decided to use Machine Learning Model in my project. Then, when developing a model, it becomes important to choose which model, augment the data, what algorithm to use, etc. To make a good choice, you need to determine whether the model is overfit or has high variance.  Professor Andrew's DeepLearning Specialization covered this and put it together.  

# What should I do if I check the error-rate?
Error-rate will be logged for train, dev, and test. To decide what to do with these log values, we need to define some terms.  

## Term
Here we will briefly explain and define the terms human-level performance and avoidable bias.

### Human-level performance
![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217819&authkey=%21AFm_FOTf9RHr9A4&width=611&height=351)

The performance of Machine Learning has over time surpassed human capabilities. However, it does not reach the theoretical peak performance and continues to get close to it. The theoretical maximum is called Bayes optimal error. Here, a person's ability is called human level performance. Bayes error is a theoretical maximum and can be calculated, but depending on the conditions, the calculation can be very complex, so it may be best to approximate it numerically. In other words, ***we cannot always know the exact value of Bayes error.*** Human level performance cannot exceed Bayes error, but human level performance is quite close to Bayes error. Therefore, human level performance is considered an approximation of Bayes error and is used instead of Bayes error. 

In machine learning, model performance can be quickly increased until it reaches human level performance. Because you can set bias and variance, you can clearly know how to set your goal. However, if it exceeds human level performance, it becomes difficult to improve performance. Since the Bayes error is not known precisely, the target point cannot be accurately determined. In particular, it is known that models that utilize **Structured Data** such as Online Ads, Recommendation, and Loan Approval have already surpassed those of humans. In this case, methods other than those using bias and variance must be prepared. However, deep learning has not yet caught up with humans in **natural cognition** such as vision, hearing, and thinking. Therefore, it can be said that it is important to know human-level performance.  

### Avoidable bias
Avoidable bias is **the difference between Bayes error approximation and training error**. Here, the approximation is human level performance. Set this avoidable bias to bias, and set variance to the difference between human level performance and dev error.
Strategies will be developed to increase the model's performance. At this time, avoidable bias must be greater than 0, implying that the upper bound of training error is human level performance.  

## bias reduction or variance reduction ?
  
|error type|Approach|
|-------------|-----------------------------------------|
|High Avoidable Bias| - Traing Bigger Model  <br/> - Train Longer  <br/> - Better Optimization Algorithm  <br/> - Architecture,hyperparameter search|
|High Variance| - More Data <br/> - regularization <br/> - Architecture,hyperparameter search|  

A high avoidable bias means that the model does not learn training data well, so you can use a model with more parameters, longer training, or a different optimization algorithm.
A high variance means that the model does not generalize well, so you can collect more data or use regularization techniques.  

# Error를 분석하자
Whether it was a bias problem or a variance problem was determined through the error-rate. If so, you need to analyze the details of the specific error and come up with a specific strategy. 

## error-label check
You can gain specific insight by having people check errors in dev, test, and train. For example, in the case of a multi-classifier problem classifying animals, you can check how misclassification was done as follows.  

|Image|Dog|Cat|....|Comments|
|-------------|-------------|-------------|-------------|-------------|
|1| |$$\checkmark$$ | |Pitball|
|2|$$\checkmark$$ | | |Lion|
|...| | | | | 

When checking, there may be cases where the labeling is incorrect. In this case, it is said that it is better to put a separate mark called label miss.  

|Image|Dog|Cat|....|miss label|Comments|
|-------------|-------------|-------------|-------------|-------------|-------------|
|1| |$$\checkmark$$ | ||Pitball|
|2|$$\checkmark$$ | | ||Lion|
|3| | | | $$\checkmark$$|miss labeling|
|...| | | | ||    

Once you gain insight through this analysis, you should not immediately start working on improving performance. Fixing everything just because a specific error or mislabeling occurs is not always the right answer. For example, we came up with 10 examples that misclassified dogs, so we set out to design an algorithm to classify dogs well. However, if this example has a 1% overall error rate, this will only lead to a negligible performance improvement. Also, the labeling was incorrect, so resources were expended to correct it. However, if this ratio is around 2%, it will not result in significant performance improvement.
If a specific example or miss labeling ratio is 30% to 40% of incorrect answers, significant performance improvement will be seen.
There are a few tips when checking error labels.

When a person checks and modifies the error label, the process that was done in the dev set must also be performed in the test set. The machine learning model evaluates the performance of the model, assuming that dev and test are data from the same distribution. Therefore, if it can be generalized in dev, it should also be generalized in test. Since dev and test must have the same distribution, if you change the label in dev, change the label in test as well.

When checking errors, it is helpful to see what you hit. This is because there is a possibility that the model got lucky and got the answer right. If only incorrect examples are checked, the model's error estimate may be overestimated. However, the better the model's performance, the more difficult it is to do. For example, if the model's accuracy is 98 percent and the error rate is 2 percent, viewing 98 percent of the data would consume a lot of resources.

In reality, it is standard to check errors in training data, but since the size of the data is considerably larger than that of dev and test, it consumes a lot of resources. Deep learning algorithms are robust to random errors, so they will have little effect on random errors in the training data. 

## Build System First
In fact, when working on an ML project, it is important to build a system that improves model performance through bias and variance rather than focusing on error analysis. It is said that it is okay to resolve error analysis or distribution differences later. First of all, it is much more important to implement a system that improves model performance. 

## Mismatched data distribution
Through the above Error Analysis process, it may be revealed that it is not a specific label or mislabel problem. It may be a problem of variance resulting from differences in distribution between train, dev, and test. 

### Distribution Eqaulity
For example, let's say you are developing an ML application that runs on a mobile app. Approximately 200,000 pieces of train data were collected from the web, and 10,000 pieces of data collected from the mobile app were divided into 5,000 pieces for dev and test. In this case, problems may arise due to differences in distribution between train, dev, and test. Image data from the web has good quality, but image data from mobile apps may have poor quality.

At this time, you can use a method that simply shuffles the train, dev, and test data to create the same distribution. However, this new data will only have a mobile data reflection rate of about 4.7 percent. The distribution that the ML model wants to learn is the mobile distribution, but the model is targeting a completely different distribution.
Instead, you can set dev and test to about 2,500 each and include 5,000 in train, making the train distribution closer to dev and test. However, the distribution of train, dev, and test is still different.

Which method to apply will vary depending on the team's situation and project constraints, so there is no right answer. However, Professor Andrew says that the latter method will show better results in the long run.   

### Data Augmentation and Collect Data
Obtaining more data can also be a good idea. At this time, data augmentation and collect data are considered.
Collect data literally means collecting additional data. If there is not enough mobile data in the train, you can collect additional mobile data and include it in the train data.
Data augmentation is synthesizing new data with existing data. For example, you can create the voice of a person speaking in a car by combining the sound of a human voice and car noise. Alternatively, you can create a new car photo from an existing car photo. However, there are some things to be careful about when performing data augmentation. **When augmenting with small data, the train is still likely to be a small subset of the sample space**. Let's assume that there are about 10,000 hours of car noise data and 1 million hours of human voice data. Here, the method that can be used is to repeat the car noise sound 100 times and synthesize it with the human voice. To the human ear, they all sound like different noise data, but from a computer's perspective, they all appear to be the same noise data. Also, if a new car image is created from 20 car images, it may look different to the human eye, but from a computer's perspective, it may be the same car image. If only a small subset is trained, the model will overfit part of the sample space.  

### Train-dev 
You may not know whether the train data, dev, and test data are from different distributions. In this case, you can check by creating a new data set called train-dev. Since train-dev is randomly sampled from train data, train-dev and train have the same distribution. Therefore, it is possible to determine whether this model is good at predicting unseen data. 

# Decision Making in General Foramt
  
|Error Type|Comapre|
|-------------|-----------|
|high avoidable bias|human-level vs train error|
|high variance|train error vs train-dev error|
|data mismatch|train-dev error vs dev/test error|  

Differences from train-dev error have been added to the decision strategy written above. Comparing whether the algorithm generalizes well with train-dev rather than dev or test can be seen as a more generalized form of decision making. 


# Conclusion 

When an ML project team improves the performance of a new ML model, it can be seen that the following sequence is followed.

1. Create a system that can apply the approach according to the bias and variance values.
2. After building the system, check whether there is a distribution mismatch through error analysis.
3. If the distribution is not mismatched, priority is given to those with a higher ratio among error cases.



# Reference 

[DeepLearning Specialization](https://www.coursera.org/learn/machine-learning-projects/home/week/1)
