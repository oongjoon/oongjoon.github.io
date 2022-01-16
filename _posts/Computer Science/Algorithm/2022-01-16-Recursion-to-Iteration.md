---

title : Recursion to Iteration

# layout: "post"

excerpt: "Recursion , Iteratoin"

categories:
  - Algorithm
tags:
  - [Algorithm,Recursion,Iteration,Computer Science]

toc: true
toc_sticky: true
---



Last time we talked about Recursion and Iteration. Recursion and Iteration are algorithms that do not substitute for each other, but change and use them according to the situation. From the point of view of Computer Architecture, Recursion and Iteration all do the same thing. Therefore, switching between each other is possible at any time. However, I looked for a general methodology for this, but couldn't find it. I will try to present my own methodology as well as an examination of the differences between the two that I obtained through trial and error. (This article may be a little less readable than usual.)



## Recursion vs Iteration

Let's compare Recursion and Iteration. First of all, what the two have in common is that when a specific subroutine ('function' in Python) is completed, the State is converted.

```python
def fact(n) :
if n == 1:
return n
return n * fact(n-1)

```

Taking the factorial function as an example, when the recursive function call is completed, it converts the state of the previous caller.

```python
a = 1
while n > 0:
a *= n
n-=1

```

In the while clause, a subroutine can be seen as code inside a while. You can see that n is decremented by 1 for every iteration.

```python
stack =[]
while n > 0 :
stack.append(n)
n-=1
a = 1
while stack :
a *= stack.pop()

```

You can also convert it like this. You can restore state using an auxiliary stack. If the restoration of the state is not fixed and changes depending on the branch, an auxiliary stack should be used. I'm going to assume the general case, so I'm going to assume you're using an auxiliary stack.

The difference between the two is the number of namespaces.

![image](https://user-images.githubusercontent.com/50165842/149150164-eb63ac0b-91d9-4e20-afbc-fbe567208a81.png)

In the case of a recursion program, a new stack frame is formed every time it calls itself. Instead of assigning to consecutive addresses, it is assigned to a random address.

```
import inspect
def fact(n) :
    if n==1 :
        print(inspect.stack())
        return 1
    return n * fact(n-1)

fact(4)

>>frame=<frame at 0x7fa1c0b5f3a0,
>>frame=<frame at 0x7fa1c0b5f1f0,
>><frame at 0x7fa1c3fc0dc0
...
```

If you look at the byte difference between stackframes, you can see that the larger n, the more overhead. It can be seen that the difference between addresses is larger than the integer size because other values other than n are stored in the frame. Actually, Python's recursion has a limit.

On the other hand, iteration creates only one namespace of global scope. These , differences make a very important and big difference.

## where to go?

Most programming languages as well as Python support the recursion concept. After executing the function (subroutine), it returns to the frame (scope) of the previous caller. This means that within each frame there is a pointer to the previous caller's frame. However, in case of iteration, there is 1 frame. There is no frame to return to. In other words, ***<u>recursion can restore the previous state by going back to the caller's frame, but iteration cannot recover the state by going back to the previous frame</u>*** . Therefore, in case of iteration, additional storage space is required if the state needs to be restored. Therefore, an auxiliary stack is required. It restores the previous state by using an additional stack.



## Steps: Recursion to Iteration

Now that you know how to restore the stack of Iteration, it's time to convert. I personally think that the process of changing from Recursion to Iteration is not that easy. Factorial is an intuitive example, and since it is an example that does not need to use a stack, I think it is easy to convert. However, I think that the example of deciding which state to restore through a branch becomes quite complicated. A typical example is binary tree traversal. You can memorize famous examples, but I think you can change it more systematically if you set a step to change iteration in Recursion and practice changing it. What I am going to introduce is not a general solution, but a step-by-step guide to what kind of process should be performed.

### 1. Write Recursion

First of all, I think the first condition is to be able to write a Recursion program. Since the Recursion program can be seen intuitively by humans, I think it would be good to start from here. The most important thing here is that when writing a Recursion program, it is better to write it strictly according to the definition rather than roughly writing it in PseudoCode. Let's recall the definition of recursion again.

- Programming technique that function call itself
  - In programming, the goal is not to have infinite Recursion
    - ***<u>must</u>*** have 1 or more base cases
    - ***<u>must</u>*** solve same problem on other input with goal of simplifying larger input

I brought back Professor Grimson's definition, where you can see the word must is used. That is, ***<u>a rule that must be followed</u>***. Recursion is branched based on the base case. It decides whether to execute the subroutine in the base case or the routine in the general case. If you don't specify this, you can't decide which state to save in the iteration. And, it is important to make sure that you are solving the same problem every time. This condition is also related to how to set the branch, so you must keep it clearly.

```
procedure inorder(node)
    if node = null
        return
    inorder(node.left)
    visit(node)
    inorder(node.right)
```

The inorder traversal will be written as above.

### 2. What state to restore

Now, if you have written Recursion strictly, you need to decide which state to restore to. You can do this relatively easily by looking at the Recursion program and creating a Branch.

For example, for tree traversal, the base case is whether the node is null or not. Therefore, the branch condition becomes as follows.

- node is null
  - base case
- node is not a null value.
  - function execution

```
procedure iterativeInorder(node)
    stack ← empty stack
    while not stack.isEmpty() or node ≠ null
        if node ≠ null

        else

```

If you look at the recursion program you wrote, you can see that it simply returns in the case of the base case. This means that the program has to do the process of restoring the state directly. In the case of inorder, if it is null when visiting a node, just visit the root and then visit the right subtree. In this case, you can simply write:

```
procedure iterativeInorder(node)
    stack ← empty stack
    while not stack.isEmpty() or node ≠ null
        if node ≠ null
        
        else
            node ← stack.pop()
            visit(node)
            node ← node.right
```

If the value is not null , the subroutine is executed. In case of recursion, the subroutine is executed again after visiting the left subtree. So, you would write something like this:

```
procedure iterativeInorder(node)
    stack ← empty stack
    while not stack.isEmpty() or node ≠ null
        if node ≠ null
            stack.push(node)
            node ← node.left
        else
            node ← stack.pop()
            visit(node)
            node ← node.right
```

If the node is null in inorder, it was not specified, but the process of restoring the state was simple. If you write your code in a simple stream of consciousness, it works just fine. However, you need to focus on this restore process. For postorder, you should focus on the restore process.

```
procedure postorder(node)
    if node = null
        return
    postorder(node.left)
    postorder(node.right)
    visit(node)
```

In the case of the postorder, the case of the base is the same as other tree traversal algorithms. So, it will be converted to an iterative algorithm as shown below. If the visited node is not null, the subroutine will be executed.

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def postorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        
        stack = []
        curr = root
        ans = []

        if root is None : return []
        while stack  or curr is not None:
            if curr is not None :
                stack.append(curr)
                curr = curr.left
            else :

        return ans
```

If the visited node is null, the case is relatively harder than inorder.

 If you look closely at the code for Inorder Traversal, if it is null, the next node to visit from the stack is popped. This is to forcefully determine which node to visit next. For inorder, always visit the top of the stack. In any case, when the base case is reached, the state of the previous caller is restored and used. However, postorder does not. You are visiting the right side of the stack, not the node on the stack.

That is, ***<u>You must know which state to restore.</u>*** ***<u>postorder has a new state, not restores the top of the stack after visiting the leftsubtree. The subroutine is executed again</u>***. After creating recursion based on the definition, you need to check which state to restore.

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def postorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        
        stack = []
        curr = root
        ans = []
        # stack.append(curr)
        lastvisitnode = None
        if root is None : return []
        while stack  or curr is not None:
            if curr is not None :
                stack.append(curr)
                curr = curr.left
            else :
                temp = stack[-1].right
                if temp is None : 
                    ans.append(stack[-1].val)
                    temp = stack.pop()
                    while stack and stack[-1].right == temp :
                        temp = stack.pop()
                        ans.append(temp.val)
                else :
                    curr = stack[-1].right
        return ans
```

We will omit the detailed details of the postorder. It is important to note that what curr points to in all branches does not change. curr can be seen as an argument value in Recursion. Argument changes in recursion when creating a new stack frame. A recursion program can create multiple stack frames, which has the same effect as having multiple branches in one program. However, iterative programs need to tell us how to recover when the call ends in all states. It is okay to write an algorithm for each quarter, but in that case, you are writing a new algorithm, not a converted recursion program. As I said earlier, we didn't solve the same problem for different inputs that get smaller each time. ***<u>In order to solve the same problem, I personally think that it is better to write a separate code that restores the state</u>***. *** In the process of restoring <u>State , I think that curr (which acts as an argument in Recursion) has a base case (null in this case) value until a new subroutine is executed to indicate that it is being restored.< /u>***

