---
title : "What is Objects and Why use Built-in objects"



excerpt: "Python Objects"

categories:
  - Python
tags:
  - [Python,Object,Builtin]
# classes : wide
toc: true
toc_sticky: true
---
# What is Objects?

To create a program using Python, you create a program using objects. It can be seen that it is natural to create a program using an object, but why do we have to use the built-in type?

## Python's Conceptual Hierarchy

To understand Python's Object-Type, you need to know what layers of Python Syntax are.

1. Program consists of several modules.
2. Modules consist of several Statements.
3. Statement consists of several Expressions.
4. Expression processes or creates Object-type.

In other words, by deduction, it can be seen that Python's programs operate only through Object-type.

In other words, programs in Python need to know how objects work, so that Python programs can behave as intended.



## Why Use Built-in types?

I understand why you need to know objects well, but why use Built-in Object Type? Can't I create the Object-Type that supports the operation I need?

### C++,C vs Python

 If you think about what C++ and C do, you can see why built-in types are used. It is mainly focused on implementing Object in C, C++. For this object, we need to lay out the memory structure, manage memory allocation, and implement the search and access routines.

However, if you use Built-in Object in Python, you don't need to do the work you have to do in C++ or C.

### Other Reasons

In addition to the above reasons, there are many other reasons.

1. For a simple task, the Built-in Object type can provide the data structure needed for the problem domain I am solving now.
2. For complex tasks, you may need to use custom objects or C language interfaces. Built-in type objects are the components of custom objects.
3. Built-in objects are more efficient than custom data structures. Python's Built-in Object uses a data structure implemented in C to optimize speed.

## Python's Core Data Type

Python's Core Data Type is as follows.

- Object
- Numbers
- Strings
- Lists
- Dictionaries
- Files
- Sets
- Other Core Types
- Program Unit Types
- Implementation-related data types
