---
title : "How to state retention in nested scope"



excerpt: "Nested Scope"

categories:
  - Python
tags:
  - [Python,Scope,LearningPython,Chp17Scope]
# classes : wide
toc: true
toc_sticky: true
---
When programming in Python, the scope is searched in the order of LEGB. The programmer does not manually manipulate the built-in scope. Actually, the scope used by the programmer to store the state can be seen as three scopes of LEG (local , encosing def, enclosing module (global)).

If the state can be stored as intended in these three scopes, the program's flexibility will be further increased.

## Nested Scope Name(State) Scope Rule

There are two ways to access state(name) in Python: reference and assignment. In combination with the global scope we saw last time, we need to know how reference and assignment are performed in the LEG scope. This time, we will look into the scope rule in Nested Scope in detail.

### Reference

Reference in python means that there is only name(X) in a statement. In Python, Name Reference Rule in Nested SCOpe proceeds in the following order.

1. currently local scope
2. lexically enclosing function , from inner to outer
3. current global
4. built-in

Here, in No. 2, from inner to outer means the following scope.

```python
def
	def
# from here up
		def

```

If a statement referencing a name is given, the scope is searched in the order of 1 to 4.

### Assignment

In python, assignment means assigning a certain value to name in a certain statement (X=value). In Python, AssignmentRule of Nested Scope proceeds in the following order.

1. current local scope (default)

Basically, in the local scope, the default is that the name is bound to the local scope. However, if namespace declaration is added, it is bound to another scope.



- if global x = value is declared inside a function
  - X exists or exists in the enclosing module (global) scope.
- If nonlocal x = value is declared inside a function
  - X exists or exists in the enclosing def (nonlocal) scope.

This nonlocal will be covered in another post later, so don't worry about it for now.

The characteristic of global declaration is that scope search starts from the global scope. Instead of looking for local, encloisng def scope, the search is performed in the order of global scope and built-in scope.

The namespace declaration creates a new name if name does not exist in the scope of the search scope. In other words, if global is declared in the enclosing module, the name exists in the global scope.

### Example

```python
X = 99

def f1():
    X = 88
    def f2():
        print(X)
    f2()
f1()
```

In this example, f2 is in scope f1. When you call f1, you create a function object f2 inside f1. This f2 exists in the scope of f1. When you print X, f2 refers to the name in the local scope of f1. According to the LEGB RULE, there is no name X in local, and X exists in the enclosing module, so X in the enclosing scope is referenced.

```python
def f1() :
    X = 88
    def f2() :
        print(X)
    return f2

action = f1()
action()
```

This example may be a pattern that you can encounter while using python. This time f1 will return f2. The moment f1 is called, f2 is not called yet. While calling action, f2 is called, and when actually called, f1 scope no longer exists, but f2 references X in f1 scope and prints it.

## State Retention in Nested Scope

In the example that returns the function above, X is alive even though the f1 scope is gone. Such a function is called a factory function. Let's see how to preserve the state of various enclosing defs as well as factory functions.

### Factory function

This operation is called a closure function or factory function. The returned function object remembers the variable in the enclosing scope. It doesn't matter if the enclosing scope is in memory or not. This can be an alternative to the Class attribute.

When a condition occurs at runtime, there may be situations where you need to create an event handler on the fly. When that event handler needs to save state, it can use a factory function.

```python
def maker(N) :
    def action(X) :
        return X**N
    return action

f=maker(2)

print(f)

print(f(3))
print(f(4))

g = maker((3))


print(g(3))
print(g(4))
```

If you look at this example, you can see that f and g each call the maker function, return the factory function, and call the function object again, f calls the square and g calls the cube. That is, each factory function You can see that each has state information.

In general, classes make attributes explicit, but factory functions implict rather than explict.

#### Class vs Closure

The comparison between Class and Closure is as follows.

| Class |                      | Closure |
| ----- | -------------------- | ------- |
| O     | inheritance          | X       |
| O     | operator overloading | X       |
| O     | methods              | X       |

If state preservation is the goal, closures can be seen as a lighter alternative to classes.



### Retain scope in python 2.X : using default

You will seldom use Python 2.X, but it may be rare. Python 2.X does not support Factory Functions. This is because, in Python 2.X version, the scope rule is as follows.

- local(f2)
- global
- built-in

Since the scope of the enclosing function is skipped, an error occurs when accessing the name of the enclosing function scope. Therefore, in these previous versions, the default argument value is used.

```python
def f1() :
    X = 88
    def f2(X=X) :
        print(X)
    f2()


f1()

def f1() :
    X = 88
    f2(X) # Forward Reference okay

def f2(X) :
    print(X)
    
f1()
```



Before argument arg enters f2, the value of val becomes the default value, so we can still refer to the value of val in f1. However, if you write a function like in the first case, it depends on the moment when the value is assigned to the default argument. The value of X is determined only after the def statement in f1 is executed. Also, this looks a bit complicated. Therefore, it is a better way to write two functions like in the second case. It actually works if f2 is called before f1 is called. To express this generally, it is expressed that the function to be defined after the function to be called in the function is called.

### Nested Scopes and lambdas

lambda can also be applied this way. lambda is an expression that creates a new function. This is used where def cannot be used, for example in a list or in a dictionary. Since a new local scope is created for a new function, the rules shown above are applied as it is.

```python
def func() :
    x = 4
    action = (lambda n : x **n) # x rememeberd , x from enclosing def
    return action


f = func()

print(f(2))

def func() :
    x = 4
    action = (lambda n , x=x: x **n) # x passed as default argument value
    return action

f = func()

print(f(2))
```

The first func can look up x in the enclosing scope. Still, the scope rule works fine. Also, you can pass the default value in scope to the argument of lambda as the default value.



### Scopes versus default with variables

```python
def makeActions() :
    acts=[]
    for i in range(5): # Tries to remember each i
        acts. append(lambda x : i ** x) # But all remember same last i
        
    return acts

acts = makeActions()


print(acts[0])


print(acts[0](2))
print(acts[1](2))
print((acts[4](2)))

print('--------------------')

```

If def , or lambda is nested in the loop of the enclosing function, the nested function can refer to the loop variable in the enclosing scope. And, the functions created in the loop have the loop variable of the iteration of the last loop.

This is because, when the nested function is called, the variable of the enclosing scope is searched. In the above example, if makeActions is assigned, i in the for statement becomes 4. And, if you call the lambda function with `acts[0][2]`, the function is executed, and i = 4 at this time.

In this case, the value in the enclosing scope should be passed as the default value.

It is a very esoteric property, but it can be used when you need to create call-back handlers (ex.



### Loop variables may require defaults, not scopes

This case is a bit strange, so if you don't understand it, you can skip it.

```python
def makeActions() :
    acts=[]
    for i in range(5) : # Diffenreec . Use default i
        acts.append(lambda x , i = i : i**x)
        
    return acts

acts = makeActions()

print(acts[0](2))

print(acts[2](2))

print(acts[4](2))

```
