---

title : Topological Ordering

# layout: "post"

excerpt: "DAG or Cycle"

categories:
  - Algorithm
tags:
  - [Algorithm,6.006,Computer Science]

toc: true
toc_sticky: true
---
When studying algorithms in Computer Science, one of the first algorithms you encounter is DFS. Topological Ordering or Topological Sort is introduced as an application example of DFS. Topological ordering is something that many Computer Science undergraduate students are confused with. Therefore, they give up trying to understand this algorithm. As interview season approaches, they will memorize answers and formulas for the interview.
However, Topological Ordering is a prerequisite for many Computer Science ideas. If you do not know this, you cannot set prerequisites, so Topological Ordering is something that a Software Engineer or Computer Scientist should know. In this article, we will find out what Topological Ordering is.



## Why DAG is important?
Being able to obtain Topological Ordering means that the given graph is a Directed Acyclic Graph. Or, you can also obtain the Topological Ordering of the vertices of the graph in which cycles exist. This means that if you traverse the vertices in the given topological ordering order, you will obtain the subgraph, which is the DAG of the given graph. To explain the sentence mentioned in the Intro again, DAG is a prerequisite for many Computer Science ideas. If you do not understand in what cases DAG should be applied, you will not be able to understand many Computer Science ideas and will just have to take them and use them.

To traverse all vertices in a DAG, you must visit the first vertex in the topological order. In topological order, you cannot visit the previous vertex from a particular vertex. This ensures that if the properties of a specific vertex are calculated, the properties of the vertex will not change in the future. When doing brute force, there is a problem of having to explore the space of exponential time. If the problem can be expressed in DAG form, the result of intermediate computation is deterministic, so it is possible to explore the space of Linear Time and solve the problem.

### Example of Dag
- Shortest path
   - All vertices must be considered when finding the shortest path. However, if you use the DAG structure, the shortest distance is deterministic, so the problem can be solved in linear time.  
- Automatic Difference
   - In DeepLearning, the chian rule is used when calculating the gradient of each parameter. This is a method of calculating the gradient by first calculating the gradient of parameters close to the output and then sequentially substituting it to the parameters close to the input. The previous gradients are deterministic. In other words, since there is no need to recalculate at the moment of calculation, automatic differntiation can be performed in the Linear Time of the parameter.


## Topological Ordering Definition
We learned why topological ordering is necessary. It is difficult to apply Topological Ordering to Real World problems based solely on the above information. In order to apply Topological Ordering in general situations, you must know the formal definition of Topological Ordering. Let's look at the definition of Topological Ordering below.

### Formal Definition
<!-- $$ O(N! \cdot N) $$ -->
> Topological Order of a Graph G = (V,E) is an ordering f on the vertices such that : every edge (u,v) $$ \in $$ E satisfies f(u) < f(v)

For example, assume graph G is V = {A,B,C,D} , E = (A,B) ,(B,D) , (C,D) , (A,C) . At this time, topological order f will have the following values.
> f(A) = 1, f(B) = 2, f(C) = 3, f(D) = 4

For (A,B), f(A) < f(B) is satisfied, for (B,D), f(B) < f(D), and for (C,D), f(C) < f. For (D) and (A,C), f(A) < f(C) is satisfied. Let us consider the following ordering f:
> f(A) = 1,f(B) = 3, f(C) = 2,f(D) = 4

The values of f(B) and f(C) have changed. However, f(A) < f(B) is satisfied for (A,B), f(B) < f(D) is satisfied for (B,D), and f(C) is satisfied for (C,D). For < f(D) and (A,C), f(A) < f(C) is satisfied.

Therefore, we can see that the topological order is not a unique order. 


## Find Topological Ordering
Now that you know the formal definition of topological ordering, if you are given a sequence of vertices (or nodes), you can know whether these vertices are topological ordering or not. If topological ordering is not given and must be obtained, all permutations of vertices must be considered. In this case, to obtain Topological Ordering, you will have an UpperBound of $$ O(N! \cdot N) $$. (The Vertex Size is set to N.) Therefore, an algorithm that can obtain this in Linear Time is needed.


### Finish Ordering
 topological ordering can be obtained in $$ O(V+E) $$ by utilizing DFS, which can search in linear time. To do so, we need to define a Finishing Order. Finishing Order lists the order in which DFS calls are completed in ascending order. Let’s find out through DFS’s psuedo code.
  ```python
  def DFS(u,finish_order) :
   for v in $$adj^{+}(u)$$ :
     if v not in visited :
       DFS(v,finish_order)
   finish_order.append(u)
  ```
Since DFS terminates after considering all outgoing edges, the node called last will finish first. If you reverse this finish order and map the index of the reverse finish order to the vertex, you can obtain a topological order.

### Proof
Let us prove it for any edge (u,v). (u,v) can be roughly divided into two cases.
#### case 1
![image](https://1drv.ms/i/c/7e81bbcd99889380/IQNwehDd1CjiS5vF-7P1bnOYAclgdntFu4yPQHeDw3JGIYI?width=1024)
In this case, DFS(u) calls DFS(v). Therefore, since DFS(v) was called first, DFS(v) terminates before DFS(u). Therefore, v is included before u in the finish order. If you reverse this, u will be before v in order. Therefore, f(u) < f(v) holds.
#### case2
![image](https://1drv.ms/i/c/7e81bbcd99889380/IQOaVP27YOkORL8q758x8uz4AZmndgB9kY4wUF8_k8BC6Yw?width=1024)
In this case, DFS(v) is called and terminated, and then DFS(u) is called and terminated. Therefore, the finish order includes v before u. Likewise, f(u) < f(v) holds.
## Is it DAG?
I learned about the formal definition of topological ordering and the algorithm for calculating it in linear time. The ultimate goal of Topological Ordering is to determine whether something is DAG. Additionally, if there is a cycle, you will want to know the part of the cycle. Using what we learned above, let's determine whether the given graph is a DAG.
### Cycle Detection

We confirmed that DAG has a topological order. If the proposition called DAG is P and the proposition that it has a topological order is Q, then $$ \neg P \rightarrow \neg Q $$
will be established. In other words, if it does not have a topological order, it must be a Cyclig Graph. Therefore, you can tell whether it is a DAG or not by checking whether the reverse finish order satisfies the topological order property. This can be checked in $$ O(V +E) $$.
### Cycle Return
In addition to simply detecting a cycle, you may be curious about which vertices are included in the cycle. In order to return this cycle, we will set up and prove the Claim below.
#### Claim and Proof
> If graph G contains Cycle, Full-DFS traverse an edge from v to ancestor of v .
Here, ancestor refers to a vertex for which the call has not yet been completed.
  Let's assume that Cycle consists of vertices (v0,v1,....vk,v0).

  For generality, let us assume v0 is the vertex that first calls the Full-DFS algorithm. Any vertex v-i will continue to call vertex v-i+1. This means that the call to v-i+1 ends before the call to v-i ends. Therefore, the call to v-k will finish before the call to v0.
  Considering edge (v-i,v-i+1), if v-i+1 has not been visited yet, edge v-i+1 will be visited.
  Considering the edge (vk,v0), v0 has already been visited, but the call has not yet ended because vk is being visited. Therefore, when considering the (vk,v0) edge in the ancestor, the traverse to v0, which is the ancestor of vk, is considered.

#### How do we do in code?

How can I apply this in code? You can apply it as follows.

```python
  def DFS(u,finish_order,anscestor,cycle) :
   ancestor.add(u)
   for v in $$adj^{+}(u)$$ :
     if v in ancestor :
       cycle = list(anscetor. deepcopy)
       cycle.append(v)
     if v not in visited :
       DFS(v,finish_order,anscestor,cycle)
   finish_order.append(u)
   ancestor.delete(u)

```
If you are considering edge (u,v) and v is the ancestor of u, you can change the code to detect the cycle.
## Summary

Today we learned about Topological Ordering.

Today’s content can be summarized as follows.

- The existence of Topological Ordering means that the given Graph is a Directed Acyclic Graph, so it can be applied to several Computer Science Ideas.
- To obtain topological ordering, the reverse of the DFS finish order must be calculated.
- To determine whether a given Graph is a Directed Acyclic Graph, check whether the Definition of Topological Ordering is violated, and to return a cycle, add code to the DFS code to check whether the ancestor is visited.

## References

[Mit Topological order](https://www.youtube.com/watch?v=IBfWDYSffUU)