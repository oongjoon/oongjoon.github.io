---
title : "Categorical variable's representation and interpretation"



excerpt: "one-way table, two-way table, bar graph, distribution"

categories:
  - Statistics
tags:
  - [Mathmathics,Machine Learning,Khan,Statistics ,cateogrical data]
# classes : wide
toc: true
toc_sticky: true
---
It is to find the distribution of any given data called Machine Learning or Deep Learning. To do this, you need an understanding of statistics. Of course, you can do deep learning without knowing statistics, but statistics are a useful tool for analyzing data and interpreting models. Therefore, studying statistics can be seen as essential in machine learning.

I am going to study by listening to the statistics lecture of khan academy and organizing it.

# Analyzing Categorical data

## Variable ,individual

| Drink             | Type | Calories | Sugars(g) | Caffeine(mg) |
| ----------------- | ---- | -------- | --------- | ------------ |
| Brewed Coffe      | Hot  | 4        | 0         | 260          |
| Caff'e latte      | Hot  | 100      | 14        | 75           |
| Caff'e latte      | Hot  | 170      | 27        | 95           |
| Cappucino         | Hot  | 60       | 8         | 75           |
| Iced brewed coffe | Cold | 60       | 15        | 120          |
| Chai latte        | Hot  | 120      | 25        | 60           |

Let's assume that these coffee data are given as an example.

Here, the individual becomes a drink. Because each drink represents individual data.

> Here, the remaining columns except for individual are variables, and each row means data.

There are two types of variables: quantitative and categorical.

> quantitive represents values that cannot fit into a list.
>
> categorical means a value with several buckets.

## analyzing One categorical variable

One categroical variable can be represented by multiple representations. We would like to find out about him.

### pictographs

![image](https://user-images.githubusercontent.com/50165842/157341172-2a5f82e2-8427-4920-bf99-af6f8437380d.png)

 Pictograph means "a method of representing data using images or symbols".

 When learning statistics for the first time, it is also a popular method because it is easy to visualize.

In the example above, 1 symbol means 5 squirrels. To express this mathematically, it is five squirrels / symbol.

 How to read the pictograph is as follows.

>  The number of symbols of squirrels participating in Jazz is 5.
> 
>  Count 5 symbols* (5 squirrels / symbol).
> 
> Symbols are reduced, so 5 * 5 squirrels = 25 squirrels

### bar charts

![image](https://user-images.githubusercontent.com/50165842/157343173-a5d33159-2e5e-4b37-87fc-632ae2e992f4.png)



In the case of a bar chart, if you have a one-way table, you can convert it to a bar chart.

When making a bar chart, the scale unit must be well determined. If the scale unit is too small, the data cannot be expressed in the bar chart. For example, if the max value of 40 is the limit, meaningful information cannot be derived because there are 3 out of 4 variables that exceed the value of 50. If the max value is 10000, it will be difficult to visually compare the four. Therefore, in the above example, the ideal scale would be a min value of 0 and a max value of 70.

![image](https://user-images.githubusercontent.com/50165842/157343194-41e847a7-c315-4e14-a142-013ceb985500.png)



### bar chart comparing two sets

A bar chart can be used to compare not only one set but also two sets.





![image](https://user-images.githubusercontent.com/50165842/157555346-be29e9aa-6a5d-4139-843c-706b907a9e5a.png)

For example, you can express a person's test score by dividing it into final and midterm.

### bar chart question

Q. What information can I ask from the bar chart?

A.

- range
- mid-range
- median
- mode
- average







## Two-way table

So far, we have only studied how to express only one variable in a table or chrt. However, two variables can be expressed in a table at once. This time, we will learn how to express two variables in a table.

> There are 100 people, 48 men and 52 women. We investigated which animals they preferred to these people. For men, 36 say they have a dog, 10 have a cat, and 2 have no animal preference. Women say that 20 have a dog, 26 have a cat, and 6 have no animal preference.

Let's try to express the above investigation in several ways.



### two-way frequency table

![image](https://user-images.githubusercontent.com/50165842/157670735-afb44c35-328d-4573-a0d4-d5663757bf3f.png)

1. The above data has two categorical variables. This variable indicates gender and preference.
2. Since gender variable has 2 buckets and preference variable has 3 buckets, you need to put values in 6 cells.
3. Make the preference variable in 3 rows and 2 columns indicate gender.
4. Enter the value corresponding to the position corresponding to (row,column).



Filling in the count as above is called a frequency table.



### venn diagram

![image](https://user-images.githubusercontent.com/50165842/157671681-76015b84-952f-4915-8cd4-521078d35d0e.png)



If each variable has two buckets, it can be easily expressed as a venn diagram.

![image](https://user-images.githubusercontent.com/50165842/157672946-efed14c6-a90c-452f-8578-0f9d4f98086c.png)







### two-way relative frequency table

![image](https://user-images.githubusercontent.com/50165842/157774712-934c24b2-8d87-4899-a297-ed4a4f24ed49.png)



You can also represent the frequency table differently. You might be wondering what the percentage (relative-frequency) for each column is, or the percentage (relative-frequency) for the rows.

It becomes possible to compare the possibilities of other variables with respect to one variable.

For example, in the data above, students who do not play sports are more likely to play instruments than students who play sports.

### interprete two-way table

![image](https://user-images.githubusercontent.com/50165842/157775529-b55e9502-a835-46c4-9df6-0ddbac2f2e11.png)

Let's take a closer look at the two-way table.

Using a two-way table, you can see how one variable changes to another. There are several ways to interpret this, and we will discuss it.

One.

With respect to the variable called Computer time, the variable called Hours/night changes as follows.

- minimal : 5 or fewer < 5-7 < 7 or more
- Moderate : 5 or fewer ~= 5-7 ~= 7 or more
- Extreme : 5 or fewer > 5-7 > 7 or more

As computer time decreases, sleep time increases, so it can be concluded that minimal and 7 or more are related.



2.

At 7 or more , the percentage of Minimal is 55%. At 7 or more, the rate of Extreme is 20%. Therefore, since Minimal is majority in 7 or more, it can be seen that there is a relationship between 7 or more and Minimal.



3.

At 7 or more, the percentage of Minimal is 55%. Minimal is 35.8% of the total. Since Minimal is not the majority of the whole, the proportion of Minimal in other usage times is not majority. Therefore, it can be seen that 7 or more and Minimal are related.

Considering this based on other variables, the proportion of 7 or more in Minimal is 51.1%, and 7 or more is 33.3% of the total, so it can be seen that Minimal and 7 or more are related.





## Marginal distribution and conditional distribution

We saw a two-way table above, which can be seen as a joint distribution because it follows two dimensions (two variables).

This time, we will look at two types of distribution among these distributions.

### marginal distribution

![image](https://user-images.githubusercontent.com/50165842/157877405-0c44a84d-ee80-4be9-a97b-a54469a481fb.png)

When there is such a two-way table, there is a part highlighted in green. This section shows how many people there are in Graduate, Undergraduate, ie each student level.

As such, seeing how many are in each bucket based on only one variable for the whole is called marginal distribution.

 The marginal distribution can be viewed as concentrating on one variable.



### conditional distribution

Q. Find the conditional distribution of A status variable for each student level





![image](https://user-images.githubusercontent.com/50165842/157877994-a24d17be-deb3-4a80-b5a7-7608df21d01e.png)



Conditional distribution means that there is a variable a, and when a certain value is given to a variable b, it is expressed as a percentage, which is called the conditional distribution of a to b.





# Reference

[ Analyzing categorical data  Statistics and probability Khan Academy](https://www.khanacademy.org/math/statistics-probability/analyzing-categorical-data)

