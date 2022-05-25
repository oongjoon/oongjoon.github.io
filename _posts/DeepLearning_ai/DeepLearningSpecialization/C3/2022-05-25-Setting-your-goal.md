---
title : "Setting Your Goal"



excerpt: "DeepLearning.ai Dls ML strategy (Metric, Data Split)"

categories:
  - DLS_C3
tags:
  - [Machine Learning,Coursera,deep learning ]
# classes : wide
toc: true
toc_sticky: true
---

# Setting your Goal

When training a machine learning model, instead of training only one model , the model that is most likely to work among several models is selected. At this time, a model is selected by a certain standard, and this standard is called Metric. It's not difficult to sort this metric and choose the best model, but if you don't know how to set the metric, you can't choose a good model.

## Metric is case by case

It is important to remember that setting the metric is case by case. Metrics vary depending on the goal the Machine Learning Project Team is aiming for. For some teams, low latency may be the most important, and several indicators may be considered at once. The team will have a meeting to decide which metrics are important to you. With this, let's learn a simple method about how to set metrics well.



## Using Single Number Metric

When multiple metrics need to be considered, there are cases where they can be combined into one number. For example, Recall and Precision are metrics for how well a model performs for a given DataSet. Using the average of these metrics, we can find a good value for . Among the averages, you can use arithmetic mean, geometric mean, harmonic mean, or create a new function that uses metrics as a parameter.



## Satisfying Conditions and Optimizing

If there is a case where latency and precision need to be considered at the same time other than the above case, it is awkward to combine these two metrics. Therefore, in this case, there is a method to select the metric with the highest priority and set the conditions that the remaining metrics must satisfy. In general, if a certain condition is satisfied for the rest of the metrics, even if they do better than that, they often don't care. Assuming that there are N metrics, if N-1 metrics satisfy the condition, the best model can be selected with the remaining 1 metric.

For example, let's say that the metrics to be satisfied are latency , precision , user response time , and memory size . If the most important metric is set to precision, latency can be set to 100ms or less, user response time to 150ms or less, and memory size to 1gb or less.



## Train / Dev / Test

### Dev Distribution vs Test Distribution

When data is collected, it is divided into Train , Dev , and Test . Train data is data used to update model weights, dev data is data that evaluates how well the trained model performs on unseen data, and test data is how well the evaluated model performs when given real world data. It is data to measure whether or not

When data is divided into three categories, if the distribution of dev and test is different, the model may show different performance from the expected performance in the real world.

For example, let's say you are creating a global face recognition model. We decided to use data from people in asia, data from africa for dev, and data from people from other regions such as europe and america for test. If this is the case, it will be a working model for asai and africa people. Therefore, the ideal setting method for the dev, test dataset here is to shuffle the data of all regions and divide it into dev and test , then good performance in the dev set will lead to good performance in the test set .



### Train , Dev , Test Split

DataSet is collected, and Train , Dev and Test are divided. If there is no Test Set, the ratio of Train and Dev is set to 7:3, and if there is a Test Set, the ratio of Train, Dev, and Test is set to 6 Setting it to :2:2 is the rule of thumb.

However, the above is a story before learning a deep learning model with big data. In the past, it was about 10,000 , but in the big data era, it is often the case that data exceeds 1 millions. Normally, when the data exceeds 1 million, there is no need to set the dev set and test set to about 200,000. If you have about 10,000 test sets and dev sets, it's fine. In other words, the ratio of the dev set to the test set is not important,but we need enough datasets to proceed with the evaluation process.



