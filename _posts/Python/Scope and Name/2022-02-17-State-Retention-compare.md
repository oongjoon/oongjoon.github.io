---
title : "State Retention Compare"



excerpt: "Summary"

categories:
  - Python
tags:
  - [Python,Scope,LearningPython,Chp17Scope]
# classes : wide
toc: true
toc_sticky: true
---
# Module scope State Retention



```python
def tester(start) :
global state
    state = start
    def nested(label) :
        print(label, state)
        state +=1
        
    return nested

F = tester(0)

F('spam')

F('ham')

G = tester(42)
```



When stored in the state of the module scope, the variable containing this state becomes a global variable. If you use the global state like this, the following problems occur.

- A `global` declaration must be applied to all code blocks (ex. function, class).
- There is a possibility of name collision in the module namespace.
- ***<u>Only the same state can be stored with one name.</u>***

The third trait is the most important. In MultiProcessing, it can be an important feature that can be used deliberately to ensure that forked processes have the same shared resource (ex.lock, mutex). However, it is not a suitable option if you need to create multiple event handlers.

When using the Global Statement, various options such as cross-file change and control flow must be considered.

Reference: [Python Global Statement and Gloal scope is dangerous - Woongjoon_AI (woongjoonchoi.github.io)](https://woongjoonchoi.github.io/python/Global-Scope/)

# Enclosing Function scope name Retention

When using the above global state, there were the following problems.

The function returns a value, but the state is preserved and not transmitted between calls.
- Most applications want to have different states.

```python
def tester(start) :
    state = start
    def nested(label) :
        nonlocal state
        print(label, state)
        state +=1
        
    return nested

F = tester(0)

F('spam')

F('ham')

G = tester(42)
```



`nonlocal` stores mutable independent states in memory. It is used in the following situations:

- When `global` is not available
- When using a class is too overkill

When using NonLocal, it is similar to global, but there are differences, so you need to be aware of it and use it. And, if you only need to reference the name in the enclosing function scope, using a factory (closure) function can be an option.

Reference: [Nonlocal statement - Woongjoon_AI (woongjoonchoi.github.io)](https://woongjoonchoi.github.io/python/Nonlocal/)

Reference: [How to state retention in nested scope - Woongjoon_AI (woongjoonchoi.github.io)](https://woongjoonchoi.github.io/python/Nested-Scope-Name(State)-Scope-Rule/)

# Class State Retention

You can also preserve the state by using a member of a class. Using class, you can use the following functions

- inheritance
- method
- etc..

Members of a class can be accessed explicitly, whereas they can be accessed implicitly by preserving the state in the enclosing function scope using nested functions.

You can solve this state problem with a class, but using a class to simply preserve one state is overkill.



# Function Attribute

If you use Functoin attribute in Python, function attribute is added to nested function even in 2.X and 3.X.

```python
def tester(start) :
    def nested(label) :
        print(label, state)
        state +=1
    nested.state = start
    return nested

F = tester(0)

F('spam')

F('ham')

G = tester(42)
```

You can access this attribute from anywhere, just like a class attribute. This is not to assign a name to the nonlocal scope, but to add an attribute to the function object called nested.

However, I think this feature is an old-fashioned relic with a lot of overlap with relatively newer features like nonlocal.

## mutable in function

```python
def tester(start):
 def nested(label):
         print(label, state[0]) # Leverage in-place mutable change
         state[0] += 1 # Extra syntax, deep magic?
 state = [start]
 return nested
```

In Python, this syntax is acceptable. It is possible to change a mutable object in the enclosing scope. However, you should avoid writing something like this with syntax that can confuse the viewer.

If you see this code in legacy code, you only need to understand how it behaves. .

