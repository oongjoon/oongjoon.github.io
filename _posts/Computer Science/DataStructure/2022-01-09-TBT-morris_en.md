---

title : Threaded Binary tree and Morris Traversal

# layout: "post"

excerpt: "Binary tree"

categories:
  - DataStructure
tags:
  - [Algorithm,Datastructure,Computer Science]

toc: true
toc_sticky: true
---
This time, we will discuss Threaded Binary Tree and Morris Traversal. Threaded Binary Tree is a DataStructure proposed to overcome the limitations of Recursive Binary Tree Traversal. Morris Traversal is a binary tree traversal algorhithm based on the idea of a Threaded Binary Tree.
Let's know What are the limitations of Recursive BinaryTree Traversal, and what idae is used to overcome these limitations.



## Recursive Binary Traversal 's Issue

### using stack in worst O(n)

If you use the Recursive Algorithm, a new stackframe namespace is created based on python. And, the name is bound to this new stackframe.

![5-2. [자료구조] 이진트리(binary tree)](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217824&authkey=%21AKrm8cD9n11IvQg&width=500&height=306)

If the binary tree is a perfect binary tree, the height of the tree will be $$O(\log n)$$. Therefore, a stackframe of at least $$ O(\log n)$$ will be created.

![Creating a right skewed binary tree in C - Stack Overflow](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217825&authkey=%21AATrPXZbRKvGR3Q&width=421&height=269)

If the binary tree is skewed, the height of the tree will be $$O(n)$$. Therefore, stackframe will be created as many as $$O(n) $$ in the worst case. If there is an environment where stackframe creation is restricted, it will be a very big issue. 

### waste space

Among the properties of the binary tree, there are $$ n + 1$$ number of null links. A simple proof of this is as follows.

> If the number of nodes is n, it is obvious that the number of pointers is 2n. It has n-1 incoming edges. Therefore, the number of null pointers is 2n - (n-1) = n+1.

In leaf nodes, the left pointer and the right pointer point to a null value. In other words, this space is wasted. Assuming that node is implemented as a struct or class, bits of $$ n+1 $$ are wasted.

### algorithm works in only root

The Tree Traversal algorithm has a very big drawback. That is, it is an algorithm that can be used only when starting from the root. For example, when performing inorder traversal, if you start from the root of the left subtree, there is no way to search the root of the right subtree. This is because there is no pointer pointing to the right subtree. Therefore, it is an algorithm that can only be used in limited circumstances.

## TBT(Threaded Binary Tree)

![스레드 이진 트리 - 위키백과, 우리 모두의 백과사전](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217826&authkey=%21AKsX0Nu0H5dSFA4&width=630&height=535)

> Threaded Binary Tree, that is, TBT came from the motivation to solve space wasted without using stack frame, and to enable traversal even if it is not a root.
>
> A simple definition of a Threaded Binary tree is as follows.
>
> > left null link -> pointing preceeder
> >
> > right null link -> pointing suceeder
> >
> > except), leftmost null link , rightmost null link
>
> Surprisingly, all of the above three problems are solved by simply defining the above three lines.
>
> By making null links point to preceeder and suceeder nodes except for leftmost and rightmost nodes, space wastage disappears. Next, we know which node to go to (suceeder), so we don't need to restore the state to the stack. Therefore, there is no need for a stack.
>
> Let's do a simulation of inorder traversal. (In order traversal, I will explain it assuming you know it.)
>
> > First, A is visited while searching the left subtree through a pointer. ans : a
> >
> > Then visit B because the right pointer points to B. ans : a, b
> >
> > Then, repeat the algorithm again. Currently , the node will be E. ans : a, b, c, e
> >
> > Visit F because E's right is root. ans : a, b, c, e, f
> >
> > Then, visit the right subtree. ans : a,b,c,e,f , g ,h ,i



### TBT Implementation

If you look at the TBT simulation, the operation of a pointer that was a null link and a pointer that was not a null link are different. Therefore, whether it was a null link or not should be expressed using additional storage space.

```c
struct TBTnode
{
	int value ;
    TBTnode * rlink;
    TBTnode * llink;
    bool r_null ;
    bool l_null;
};

// It's been a while since I've written in C language, but I don't know if the grammar is correct.. ㅠㅠ
```

If you tell us whether each rlink and lllink are null values with two 1-bit values, you can proceed with different operations.

### TBT traversal 

TBT traversal is similar to iterative Indorder.

```python
## psuedocode
## It's a bit small, but it's a feeling.
while :
    1. if l_null is not null :
        curr = curr.llink
        continue
    2. visit root
    3. if r_null is not null : 
        curr = curr.rlink
        continue
    curr = curr.rlink
```

Being iterative, you can see that inorder traversal without a stack could be an implementation.

### TBT analysis

However, this is not always a perfect data structure. It has the following disadvantages.

#### DisAdvantages

- Insert/Delete operation is too difficult and complicated.
- Extra space is required for boolean values.

If so, when should I use this TBT?

#### When to use it

- Insert/Delete operations rarely occur.
- Stack space is limited.

In my personal opinion, it felt like a read-only Datastructure.

## Morris Traversal

As mentioned above, constructing TBT is very tricky and difficult. When Coding Interview asked to implement Inorder Traversal with a space complexity of $$O(n)$$, constructing TBT is very inefficient. Assuming that you are solving the Coding Interview problem, in this case, the premise is that you write and discard the tree. In other words, it doesn't matter if you transform the Tree. (You should ask whether you can transform the Tree or not.)

The idea is simple. It is to make this tree into a skewed tree. The approximate psuedocode is as follows.

```
1 . go to current left node
2. go to rightmost node , rightmost node right link point to current node
3. curr left link point to null
4 . if left node is null , visit curr node
5 .if left and right all null ,terminate algorithm
```

If we repeat the above algorithm infinitely, we get the result of inorder traversal without stack.

![Morris Traversal · LeetCode](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217827&authkey=%21AOOTvTfWXz2Jg_Y&width=2496&height=1060)



Please kindly attach a picture. Visit the nodes in the same order as above.

We will provide psuedocode implemented in more detail than the above psuedocode.

```
while :
	left child exist:
		morr <- curr.left
		mor  <- rightmost(morr)
		morr.right -> curr
		curr <-  curr.left
		morr->right->left -> curr
	left child none  and right is not NOne:
		ans <- curr
		curr <- curr.right
```

[CodingTest/94. Binary Tree Inorder Traversal3.py at main · woongjoonchoi/CodingTest (github.com)](https://github.com/woongjoonchoi/CodingTest/blob/main/leetcode/explore/binary_search_tree/inorder_traversal/94. Binary Tree Inorder Traversal3.py)

The above link is on my github and I have implemented the leetcode morris traveral issue. You can refer to it for reference.
