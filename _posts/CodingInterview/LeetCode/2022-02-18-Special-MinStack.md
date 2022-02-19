---

title : Special Stalck

# layout: "post"

excerpt: "Special Stack "

categories:
  - CodingInterview
tags:
  - [Algorithm,CodingTest,CodingInterview]
# classes : wide
toc: true
toc_sticky: true
---
A data structure called Stack is a data structure with the principle of FIFO (First In First Out). The most commonly used place is computer memory. While storing elements in the Stack, you may want to keep properties such as max and min of the data set together. The existing Stack does not keep properties such as max and min. For example, in DataStructure, there is a data structure called Heap that returns the minimum value with O(1) time complexity regardless of data size. However, it is different from Heap, but in Stack, it is possible to return the minimum value with a time complexity of O(1) regardless of the size of the data.

# Problem Statement

**Question:** Design a Data Structure SpecialStack that supports all the stack operations like push(), pop(), isEmpty(), isFull() and an additional operation getMin() which should return minimum element from the SpecialStack. All these operations of SpecialStack must be O(1). To implement SpecialStack, you should only use standard Stack data structure and no other data structure like arrays, list, . etc. 

link : [[Special Stack Practice  GeeksforGeeks]](https://www.geeksforgeeks.org/design-and-implement-special-stack-data-structure/)

Design a data-structure **SpecialStack** that supports all the stack operations like **push()**, **pop()**, **isEmpty()**, **isFull()** and an additional operation **getMin()** which should return **minimum** element from the SpecialStack. Your task is to **complete all the functions**, using stack data-Structure.


**Example 1:**

```
Input:
Stack: 18 19 29 15 16
Output: 15
Explanation:
The minimum element of the stack is 15.
```

 


**Your Task:**
Since this is a function problem, you don't need to take inputs. You just have to complete 5 functions, **push()** which takes the stack and an integer x as input and pushes it into the stack; **pop()** which takes the stack as input and pops out the topmost element from the stack; **isEmpty()** which takes the stack as input and returns true/false depending upon whether the stack is empty or not; **isFull()** which takes the stack and the size of the stack as input and returns true/false depending upon whether the stack is full or not (depending upon the
given size); **getMin()** which takes the stack as input and returns the minimum element of the stack. 
**Note:** The output of the code will be the value returned by **getMin()** function.


**Expected Time Complexity:** O(N) for getMin, O(1) for remaining all 4 functions.
**Expected Auxiliary Space:** O(1) for all the 5 functions.


**Constraints:**
1 ≤ N ≤ 104

# Simple no efficient sol

It is important to remember that the min value is the min value of an element in the stack. If you think simply, you may think that it is okay to update min_value every time you perform an operation on the stack. However, min_value is initially set and if you repeat push and pop operations, min_value changes every time. It is not possible to decide whether to restore to the previous min_value while popping. There is no way to remember the old min_value. Therefore, this method cannot be used.



# Sol1

Looking at the hints above, we can see that we only need to solve the previous min_value problem. The elements of the stack change during push and pop operations, but not remembering the previous value means that there is no mapping relationship between the elements of the orignal stack and min_vaule. If the mapping relationship between the orginal stack and min_value is maintained, min_value will be a sequence containing multiple values. And, stack operation will be applied to maintain the mapping relationship.





```python
# Python3 program for the
# above approach
# A simple stack class with 
# basic stack funtionalities
class stack:
 
  def __init__(self):
 
    self.array = []
    self.top = -1
    self.max = 100 
 
  # Stack's member method to check
  # if the stack is empty
  def isEmpty(self):
 
    if self.top == -1:
      return True
    else:
      return False 
 
  # Stack's member method to check
  # if the stack is full 
  def isFull(self): 
     
    if self.top == self.max - 1:
      return True
    else:
      return False  
 
  # Stack's member method to
  # insert an element to it  
 
  def push(self, data):
 
    if self.isFull():
      print('Stack OverFlow')
      return
    else:
      self.top += 1
      self.array.append(data)    
 
  # Stack's member method to
  # remove an element from it
  def pop(self):
 
    if self.isEmpty():
      print('Stack UnderFlow')
      return
    else:
      self.top -= 1
      return self.array.pop()
 
# A class that supports all the stack 
# operations and one additional
# operation getMin() that returns the
# minimum element from stack at
# any time.  This class inherits from
# the stack class and uses an
# auxiliary stack that holds
# minimum elements 
class SpecialStack(stack):
 
  def __init__(self):
    super().__init__()
    self.Min = stack() 
 
  # SpecialStack's member method to
  # insert an element to it. This method
  # makes sure that the min stack is also
  # updated with appropriate minimum
  # values
  def push(self, x):
 
    if self.isEmpty():
      super().push(x)
      self.Min.push(x)
    else:
      super().push(x)
      y = self.Min.pop()
      self.Min.push(y)
      if x <= y:
        self.Min.push(x)
      else:
        self.Min.push(y) 
 
  # SpecialStack's member method to 
  # remove an element from it. This
  # method removes top element from
  # min stack also.
  def pop(self):
 
    x = super().pop()
    self.Min.pop()
    return x 
 
  # SpecialStack's member method
  # to get minimum element from it.
  def getmin(self):
 
    x = self.Min.pop()
    self.Min.push(x)
    return x
 
# Driver code
if __name__ == '__main__':
   
  s = SpecialStack()
  s.push(10)
  s.push(20)
  s.push(30)
  print(s.getmin())
  s.push(5)
  print(s.getmin())
 
# This code is contributed by rachitkatiyar99
```



1. Original Stack, MinStack 을 생성합니다. 
2.  When Push to original stack
   1. If stack empty : 
      1. Push(element) to minstack
   2. if stack not emtpy :
      1. if top of minstack <= element : 
         1. Push minstack top to minstack
      2. else :
         1. Push element to minstack
3. When Pop from minstack 
   1. Pop from minstack



In the case of pop operation, minstack and original stack always have a corresponding relationship, so when the original stack pops, pop together.







# Sol2

The problem with So1 is that the space of minstack causes O(n) space complexity. Is it necessary to map the minimum value to every element in the stack? In Sol1, it can be seen that the element's value changes at the moment the min value is updated in the element of Minstack. Therefore, when minvalue is updated in the original stack, if you know what min_value is mapped to in minstack, you can always get min_value. 

```python
class MinStack:
    
    def __init__(self):
        self.stack = []
        self.min_stack = []
    def push(self, val: int) -> None:
        self.stack.append(val)
        if len(self.min_stack)==0 or val <= self.getMin() :
            self.min_stack.append(val)
    def pop(self) -> None:
        if len(self.stack) == 0 or self.stack.pop() == self.getMin() :
            self.min_stack.pop()

    def top(self) -> int:
        return self.stack[-1]

    def getMin(self) -> int:
        return self.min_stack[-1]


# Your MinStack object will be instantiated and called as such:
# obj = MinStack()
# obj.push(val)
# obj.pop()
# param_3 = obj.top()
# param_4 = obj.getMin()
```

1. Push
   1. if stack size == 0:
      1. push to minstack
      2. push to original stack
   2. original stack size > 0 :
      1. push to org stack
      2. if getmin() <= ele :
         1. push to min stack
2. Pop
   1. if stack.size < 0 :
      1. not Operation

   2. if stack.size > 0 :
      1. if getmin() == orignal stack.pop() 
         1. minstack.pop()


## Q1 . Why duplicated min value ? 

If you look at the algorithm, you can see that the same min value is duplicated and stored in the min stack. It can be seen that storing only one min value is efficient in terms of space complexity. I explained above that the previous min value can be restored by mapping the min value to the elements of the stack. If you don't store the min value repeatedly, you don't know when to pop from the min stack.

Specifically, if we divide the cases,

- For unique min_value:
  - Just follow the algorithm described above.
- For multiple min_values:
  - You must indicate which element is the first min_value.

In other words, a situation arises where two mappings need to be implemented. That is, a variable that indicates whether a multiple min_value or not is added to the minstack every time, O(n) overhead is additionally generated in space complexity. Therefore, it can be viewed as inefficient



# Sol3

To summarize the above two solutions, you need to create a 1:1 mapping between min_value and elements of the stack. Is additional storage space necessary to create 1:1 mapping?

By map we mean function. A function is a relation that, if there is a key value, corresponds to the value value. If a given key value can be mathematically defined, the auxiliary space complexity of O(n) will not occur. If we put mathematically defined values on the stack, it would be possible to preserve two values without auxiliary space complexity.

## ver 1

```
class stack:
    def __init__(self):
        self.s=[]
        self.minEle=None
        
    def push(self,x):
        if self.minEle == None :
            self.minEle = x
            self.s.append(x)
            
        elif self.minEle > x :  
            self.s.append(2*x - self.minEle)
            self.minEle = x
        else :
            self.s.append(x)
        #CODE HERE
        
    def pop(self):
        if len(self.s) == 0:
            return -1
        if len(self.s) == 1 :
            self.minEle = None
            return self.s.pop()
        if self.minEle > self.s[-1] :
            x = self.minEle
            self.minEle = 2 * self.minEle - self.s.pop()
            return x
        else :
            return self.s.pop()
        #CODE HERE
        

    def getMin(self):
        #CODE HERE
        if len(self.s) == 0 :
            return -1
        return self.minEle

#{ 
#  Driver Code Starts
#contributed by RavinderSinghPB
if __name__ == '__main__':
    t = int(input())
    for _ in range(t):
        q = int(input())

        arr = [int(x) for x in input().split()]

        stk=stack()  

        qi = 0
        qn=1
        while qn <= q:
            qt = arr[qi]

            if qt == 1:
                stk.push(arr[qi+1])
                qi+=2
            elif qt==2:
                print(stk.pop(),end=' ')
                qi+=1
            else:
                print(stk.getMin(),end=' ')
                qi+=1
            qn+=1
        print()

# } Driver Code Ends
```





Since min_value must be calculated with O(1) complexity and derived as a result, it seems to be necessary to create a function that has min_value as a key.

When an element enters the stack, it is one of the relation of min_value > element , min_value <= elment.

The algorithm is as below.



1. ​	Push	 
   1. if min_value == None :
      1. min_value = element
      2. stack.push(element)
   2. min_value <= element :
      1. push element stack
   3. min_value > element :
      1. push 2*element - min_value
      2. min_value = element
2.  Pop
   1.  if stack size == 0 :
      1. return -1
   2. if stack size == 1 :
      1. min_value = None
      2. stack.pop()
   3. min_value > stack.top() :
      1.  min_value = 2 * min_value -  stack.top() 
      2. stack.pop()
   4. min_value < stack.top() 
      1. stack.pop()



Unlike before, duplicates of min_value are not allowed. This is because, for the first min_value, the mathmatical function value is calculated and pushed to the stack, so the location can be found without a separate flag.

When explaining the overall algorithm, if min_value <= element, push is done like the existing stack. If , min_value > element occurs, calculate 2 * element - min_value and push it to the stack. We need a reference point to know that an element on the stack represents a function relationship with min_value. If you want to use a criterion that is different from a criterion that is min_value<=element, you need a criterion that is min_value > element. At this point min_value is the updated min_value. So, before updating the element with min_value, we need to create f with element < f(element,min_value). Since element < min_value , element-min_value < min_value is satisfied. However, overflow may occur because the difference between the two is not known. Therefore, by using element- min_value < 0, a relationship of element -min_value + element < min_value can be derived.



## ver2

Can't it be possible to return a set of values other than one value with one key? You can use a modular operation that is often used in discrete math. Modular operation is a mapping relationship that finds remainder through a certain key value. Being able to find remainder means that we can get the quotient for the key value. Thus, it is possible to create a 1:2 mapping relationship.

```python
class stack:
    def __init__(self):
        self.s=[]
        self.minEle=None
        self.key = 9999
        
    def push(self,x):
        if self.minEle == None :
            self.minEle = x
            self.s.append(key * x + min_Ele)
           
        else :
            self.s.append(key * x + min_Ele)
        #CODE HERE
        
    def pop(self):
        if len(self.s) == 0:
            return -1
        if len(self.s) == 1 :
            self.minEle = None
            return self.s.pop() // key
        else :
            return self.s.pop() // key
        #CODE HERE
        

    def getMin(self):
        #CODE HERE
        if len(self.s) == 0 :
            return -1
        return self.s.top() % key

#{ 
#  Driver Code Starts
#contributed by RavinderSinghPB
if __name__ == '__main__':
    t = int(input())
    for _ in range(t):
        q = int(input())

        arr = [int(x) for x in input().split()]

        stk=stack()  

        qi = 0
        qn=1
        while qn <= q:
            qt = arr[qi]

            if qt == 1:
                stk.push(arr[qi+1])
                qi+=2
            elif qt==2:
                print(stk.pop(),end=' ')
                qi+=1
            else:
                print(stk.getMin(),end=' ')
                qi+=1
            qn+=1
        print()

# } Driver Code Ends
```

1. Push
   1. if min_ele == None :
      1. min_ele = element
      2. push key * element + min_ele
   2. else :
      1. push key * element + min_ele 
2. Pop
   1. if len(stack) == 0 :
      1. return -1
   2. if len(stack ) == 1 :
      1. return stack.pop()//key
      2. min_ele = None
   3. else :
      1. return stack.pop()//key
3. getmin
   1. return stack.top() % key
