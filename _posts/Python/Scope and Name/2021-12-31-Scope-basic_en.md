---
title : "Python Scope"



excerpt: "Scope"

categories:
  - Python
tags:
  - [Python,Module,Package , Scope]
# classes : wide
toc: true
toc_sticky: true
---
## Scope

Create functions, create modules, and create packages in Python. When a module (or a simple script) is executed, several variables, namely, names are assigned to the namespace. So far, I have classified these processes into two categories: local variables when they are created inside functions, and global variables outside functions. In order to use Python in more detail, I would like to explore these processes in more detail.

## name and scope

In Python, the term scope means namespace. When a value is assigned to a variable in Python, the name starts to exist from that moment. This is why you have to initialize it first when using a variable in Python. Since name cannot exist before name is declared, the location to which name is assigned in Python determines which namespace it belongs to.

## namespace

In Python, functions create an additional namespace by default. The name assigned within the function is related to the function. To reiterate this,

- The name assigned in def can only be used in the namespace of def. Otherwise, it cannot be used in namespaces.
- Also, the name assigned in def does not collide with the name in the namespace outside of def. For example, name X outside def and name X inside def are different names.


In namespace, the inside of def is called local namespace, the outside of def is called global namespace, and the inside of def surrounding def is classified as non local namespace.



```python
X= 100
def a() :
	X= 98
```

For example, if the above variables are named X, X=98 outside of def creates a local variable named X, and X =100 creates a global variable with the name X.

## Scoping Rule

In Python, namespaces have rules.

- The module that surrounds the whole is global scope.

  - This is the namespace to which the names of variables in the model file to be executed belong.
  - Global variables can be used outside the module as an attribute of the module.

- One global namespace exists in each file.

  - Do not think that there is only one global namespace when running Python. A global namespace exists for each module individually.

- When a function is newly called, the name in the function is assigned to the local namespace.

  - It's okay to think of def ,lambda as syntax to create a new local namespace.
  - In Python, a function can iterate over itself by calling it (Recursion ). That is, when a function is called, a new local namespace is created within it.

- Names assigned in the local namespace belong to the local namespace unless declared as global or non-local.

  - A name in the global namespace can be used in the local namespace through the global keyword.

  - When there is def in def, if you want to use the name in the namespace surrounding the def in the innermost def, you can use the non local keyword.

This is called the LEGB Rule in the book I referenced. L stands for local, E stands for enclosing defs , G stands for global, and B stands for built in.

## Reference

[1] Learning python chp17 Scope :  [Learning Python chp 17](https://www.google.co.kr/url?sa=t&amp;rct=j&amp;q=&amp;esrc=s&amp;source=web&amp;cd=&amp;cad=rja&amp;uact=8&amp;ved=2ahUKEwjZ7d_Fj471AhXLdXAKHYArDi8QFnoECAQQAQ&amp;url=https%3A%2F%2Fwww.amazon.com%2FLearning-Python-Powerful-Object-Oriented-Programming-ebook%2Fdp%2FB00DDZPC9S&amp;usg=AOvVaw3OQRO0BHmts707N8L_VY0O)

