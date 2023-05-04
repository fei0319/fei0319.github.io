---
title: Boruvka 算法
date: 2021-03-26T11:38:23+08:00
tags: ["最小生成树"]
categories: ["图论"]
math: true
---

Borůvka 算法是一种基于贪心的最小生成树算法，它比 Prim 算法和 Kruskal 算法更加古老。在 Borůvka 算法的基础上，已经发展出了[线性的最小生成树算法](/files/a-randomized-linear-time-algorithm-to-find-mst.pdf)。

Borůvka 算法的思想是：每次迭代，选取每一个连通块中最小的连向另一个连通块的边，将其加入最小生成树。其过程如下伪代码所示：

$$
\begin{array}{ll}
1 & \textbf{Input.}\text{ Edge set }E\text{ and vertex set }V\text{ of the graph}\newline
2 & \textbf{Output.}\text{ A minimum spanning tree of the graph}\newline
3 & \textbf{Method.}\newline
4 & \textbf{Function}\text{ Borůvka(void)}\newline
5 & \qquad S\text{ is a graph of vertex set }V\text{ and empty edge set}\newline
6 & \qquad\textbf{while}\text{ the count of connected components in }S>1\newline
7 & \qquad\qquad T\text{ is an empty set}\newline
8 & \qquad\qquad\textbf{for}\text{ each connected component }C\text{ in }S\newline
9 & \qquad\qquad\qquad\text{edge }(u,v)\text{ is the minimum edge that connects }C\text{ and another component}\newline
10 & \qquad\qquad\qquad\textbf{if }(u,v)\notin T\newline
11 & \qquad\qquad\qquad\qquad T\gets T+(u,v)\newline
12 & \qquad\qquad\textbf{for}\text{ each edge }(u,v)\in T\newline
13 & \qquad\qquad\qquad S\gets S+(u,v)\newline
14 & \qquad\textbf{return }S\newline
15 & \textbf{End}\newline
16 & \textbf{return }\text{Borůvka()}
\end{array}
$$

对于每次迭代，迭代后的每个连通块至少包含两个迭代前的连通块，故连通块总数至少减少一半，迭代次数为对数级别。

可以发现，对于每次迭代，若 $T$ 中的某些边可以连接若干个连通块形成环，则该环上属于 $T$ 的所有边的边权一定相等。因此只要确保所有边权互不相等我们就可断定 Borůvka 算法必然不会成环。

实际情况中可能会出现一些边边权相等的情况，但我们可以给边随意分配一个第二关键字，或是用并查集排除形成环的边。因此，Borůvka 算法可以用于存在边权相同的图。