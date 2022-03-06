---
title : "what is vector and operating on vectors:Computer Science perspective"



excerpt: "What  does on vectors?"

categories:
  - Linear Algebra
tags:
  - [Mathmathics,Machine Learning,Coursera,Linear algebra ,vectors]
# classes : wide
toc: true
toc_sticky: true
---

A Vector is often thought of as something with a direction. In physics, if we express the speed of a car as a vector, it is easy to accept because there are actually, forward, up, and sideways directions. In Machine Learning, Input is also called Vector, and from this point on, it tends to conflict with the existing concept. For example, when the mean of a certain group is included in the input, it is not clear how the direction of the mean is. What is a vector in Machine Learning, and what you need to know about these vectors will be briefly summarized.



# What is vector?

What is a vector? From the point of view of physics or engineering, a vector is an object moving in space, where space is a space in the physical sense. For example, when a car moves in space, it can be viewed as a vector.

The meaning of vector in computer science is slightly different. In computer science, a vector is a one-dimensional array data structure. From the point of view of physics or engineering, a vector is an object that moves in physical space, but from the point of view of computer science, it is an object that moves in data space. Therefore, a vector is sometimes called a list of attributes. For example, a household can be expressed as a vector, that is, a list consisting of attributes such as income, property, and number of members.

I am trying to apply the list of attribute to the theory of vector used in physics or engineering.

# Vector Operation

A vector can be calculated based on two rules: addition and multiply by scalar.

## addition

![image](https://user-images.githubusercontent.com/50165842/156884955-1e02a119-0fac-40b4-af69-e2f8c3c70e01.png)

1. Think of a Vector as a geometric object that thinks from the origin, and create a random vector r.

![image](https://user-images.githubusercontent.com/50165842/156885010-a9bc856c-6892-46bc-b770-149cf7840729.png)

2. Get another vector s.

![image](https://user-images.githubusercontent.com/50165842/156885062-b620782b-6e16-4e6a-9533-d2c1ffd83be5.png)

3. Move s to the end of r.

![image](https://user-images.githubusercontent.com/50165842/156885096-6a7ccaef-a0bd-4995-baaa-903ac6150af0.png)

4. Get sum followed by r followed by s. This is called r + s.



![image](https://user-images.githubusercontent.com/50165842/156945221-7cd648e9-814a-47b9-aa60-2b71d6cc46e9.png)

5. Repeat steps 1 to 4 in a different order to create a vector that follows s followed by r. This is called s +r .
6. Here, we can see that the result of r+ s , s+r is the same. So, r+s = s+r.

Here, as you can see, you get the same result no matter which order you add.

In vector addition, the left operand is the standard and we have proven this property by shifting the right operand.



## Multiply by scalar

![image](https://user-images.githubusercontent.com/50165842/156945397-22b89bef-8a3b-4165-ba15-732b7fe9f545.png)



Any arbitrary vector has r. Let's connect these two more. This will result in a vector called 3r .

![image](https://user-images.githubusercontent.com/50165842/156945497-876b8e60-801a-4f0b-bdbb-94fb2359a39e.png)



If we make this vector look in the opposite direction to be a straight line, this vector is called -r.

# Define Coordinate System

## coordinates

If you define the above two operations, you can define a coordinate system with two vectors.

![image](https://user-images.githubusercontent.com/50165842/156945777-17928489-ebbd-4873-92da-1ff7596cb3a1.png)

1. Define vectors i and j with two unit lengths.

   

![image](https://user-images.githubusercontent.com/50165842/156945883-409ce933-916c-4e9a-b103-af2c35ffa784.png)



2. 3 becomes 3i and 2 becomes 2j.
3. 3i is i multiplied by scalar 3, and 2j is j multiplied by scalar 2.
4. It is expressed using vector sum as r = 3i + 2j.

## sum in coordinate system

![image](https://user-images.githubusercontent.com/50165842/156946142-17578cef-4086-49cf-99a3-7cf0542380bd.png)

 Let's define another vector s = -1 * i + 2 * j and define ,r+s.

1. First we move the vector s to the end of r.
2. The r+s vector can be obtained using the vector sum rule.
3. r+s = (-1+3) *i + (2+2) j.
4. r + s = 2*i + 4 * j = [2,4]

What you can see here is that when you do vector addition in the coordinate system, you just add the components.

Since the operation of adding a component is just an operation of addition, the associative property has already been proven. Therefore, vector sum is associative.

(r+s) +t = r + (s+t)

## mutliply by scalar in coordinate system

![image](https://user-images.githubusercontent.com/50165842/156945883-409ce933-916c-4e9a-b103-af2c35ffa784.png)

1. We want to multiply the vector r by scalar 2.
2. 2 * r is 2x3i , 2 x 2j .
3. That is, we double the component, so 2r = [2x3,2x2] = [6,4].



1. Conversely, if we try to multiply vector r by scalar -1, -1 * r will be =[-3,-2] .



## subtraction in coordinate system

1. r+ (-r) = [3-3,2-2] =[0,0]

This is called vector subtraction. Vector subtraction is the addition of multiplied by -1 scalar.

![image](https://user-images.githubusercontent.com/50165842/156946917-7e2e9fd6-ebb1-4514-ad90-157a86957692.png)

A vector called r-s is a vector sum, so you can add components.

This will result in 3i+i = 4 , 2j -2j =0 .



# Application in Computer Science vector

For example, let's say we have a vector called house. This vector is a list consisting of attributes such as width, number of beds, number of bathrooms, and price.

When a house is , [120,2,1,150] , two identical houses are the addition of this vector, or multiply by scalar 2.

The two houses will have an attirbute of 240 sqm, 4 bedrooms, 2 bathrooms price 300.

This can be applied to finding 3 or 4 houses, and it can also be applied to adding houses of different shapes.

That is, the operation defined for the vector is applied to the house object.





#  Reference

[Mathematics for Machine Learning: Linear Algebra - 홈 Coursera](https://www.coursera.org/learn/linear-algebra-machine-learning/home/week/1)

