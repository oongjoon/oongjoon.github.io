---
title : "Python Global Statement and Gloal scope is dangerous"



excerpt: "Global Scope"

categories:
  - Python
tags:
  - [Python,Scope,LearningPython,Chp17Scope]
# classes : wide
toc: true
toc_sticky: true
---
A global variable is a name that belongs to the global scope, as explained in the previous article. In the past, when using a global variable, I knew that it could be used by using global inside a function. If you use the global statement, you can easily access the global variable in the local scope. However, if a clear understanding of the global scope is not supported, it can lead to dangerous results.

## Global Statement

In Python, the global statement is not a type or size declaration statement. It is a namespace declaration statement. That is, inside a function, you declare that you want to change a name in the global scope (enclosing moduel scope). Global statements have the following properties:

- A global name can be a reference without a global statement inside a function.
- To assign an assignment to a global name inside a function, the global name must be a declaration.

Let's look at a concrete example of this.

```python
X = 88 # Global X
def func():
 global X
 X = 99 # Global X: outside def
func()
print(X) # Prints 99
```

Since the global declaration is used inside the function, you can assign to X.

```python
y, z = 1, 2 # Global variables in module
def all_global():
 global x # Declare globals assigned
 x = y + z # No need to declare y, z: LEGB rule
```

Because y and z are global inside the function, they are not declared, so they are global scope. It will find y,z in the global scope by the LEGB look rule. Because x is declared as global, it is in the global scope.

## Be careful with using global variable

We have seen how the scope works when using the global statement. Now, you can start writing programs that do various things with the global statement, but you need to be aware of the dangerous patterns that can occur when using global names.

### Minimize global name in function

Modules and functions can use global statements to exchange some state, but this is not recommended. It is desirable that function communicates with the module through argument and return values. Let's look at an example and explain.

```python
X = 99


def a():
    global X
    X = 32
def b() :
    global X
    X= 56
    
# Q .what is value of X?
# A. It's dependent on what you calledd

b()
print((X))
```

What will be the value of X in this program? Calling a will yield 32 , and calling b will yield 56 . That is, it depends on which function is called first. This will cause the following problems.

- A global variable is affected by an external call.
- You need to know the entire flow to know the code flow.
- You need to know the entire flow to modularize this code.
- This program is function dependent.

The above example is simply a program to output the X value, so it can be rewritten as follows.

```python
X = 99


def a(X):
    
    X = 32
    print(X)
def b(X) :
    
    X= 56
    print(X)
    
# Q .what is value of X?
# A. It's dependent on what you calledd

b()
```

Here, it should be noted that our goal is not to preserve state. If the state needs to be preserved, global variables can also be a good option. In OOP, the most direct way is to preserve the state as a global variable.

There may be multithreading when a global variable is used to preserve state.

1. Single Module updates global variable.
2. According to the global variable value, parallel processing is performed using multi-threading.

If you do not know all the cases for scope, it is known that it is standard not to use global.

### Minimizing Cross file change

```python
# first.py
X = 99


```



```python
# second.py
import first

print(first.X)
first.X=88
```

There are two files named `first` and `second` . I am trying to import `fisrt` from `second` and use it. Here, you can see that `fisrt.X` is accessing the global scope of `first`. In this case, the global scope of the module becomes the namespace attribute of the module object. Therefore, when a module is imported, the importer can access all global variables of the file.

Now, in the above example, the assignment is done from `second` to `first` X, which is very implicit. Because it is changed in a module other than `first` , it is very difficult to know this in the first module. Since the two modules are intertwined, the two modules are intertwined. It is easy to see that this violates the OOP concept of encapsulation.

To be more specific, as long as direct access is possible, it can be replaced with any value. Because it can be changed to an invalid state, it is said to be implicit. Let's change this to be more explicit assignment through function.

```python
X = 99

def setX(num) :
    global X
    # X = 88
    X = num
```

```python
import first

first.setX(num)
print(first.X)
```

Since access is only possible through a function, it is impossible to change X arbitrarily. These OOP concepts will be discussed again later.
