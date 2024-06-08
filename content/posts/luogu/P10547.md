---
title: "luogu/P10547 [THUPC2024] 排列游戏"
date: 2024-06-03T19:45:40+08:00
draft: false
math: true
tags: ["计数"]
categories: ["题目"]
---

> 对于一 $1$ 至 $n$ 的排列 $p$，若交换 $p_i$ 和 $p_j$ 的代价为 $|i - j|$，则定义 $p$ 的值为将 $p$ 变为 $1, 2, \ldots, n$ 的最小代价。
>
> 求能够由 $1, 2, \ldots, n$ 进行恰好 $n$ 次交换得到，且值小于等于 $m$ 的排列的个数。

显然 $p$ 的值小于等于 $m$ 等价于 $\sum\limits_{i=1}^n |p_i - i| \le 2m$。其必要性显然，充分性可由直接沿置换环交换证明。

对 $\sum\limits_{i=1}^n |p_i - i| \le 2m$ 进行转化后可得：$n$ 个点分别位于数轴 $1, 2, \ldots, n$ 处，计算每个点出度入度均为 $1$ 且总边长不大于 $2m$ 的连边方案数。考虑前 $i$ 个点，若有 $x$ 条边起点终点都在前 $i$ 个点，则有 $j = i - x$ 条边从后 $n - i$ 个点连向前 $i$ 个点，$j$ 条边从前 $i$ 个点连向后 $n - i$ 个点。

将起点终点都在前 $i$ 个点的边称为内边，起点终点中恰好有一个在前 $i$ 个点的边称为外边（恰好有 $2j$ 个），可以设计动态规划状态：$dp_{i, j, k}$ 表示前 $i$ 个点共有 $j$ 条外边，且内边长度以及外边在第 $i$ 个点之前的长度的和为 $k$ 的方案数。这样有转移方程 $dp_{i, j, k} \rightarrow dp_{i + 1, j - 1 | j | j + 1, k + 2j}$ ($j \pm 1 \ge 0$)，可以 $O(n^2m)$ 求解。

进一步研究，发现对于任意满足 $dp_{i, j, k} \neq 0$ 的 $i, j, k$，都有 $k \ge j(j + 1)$，故 $j$ 这一维只需要枚举到 $\sqrt{m}$ 级别，复杂度优化为 $O(nm\sqrt{m})$，可以通过本题不限制交换 $n$ 次的版本。在状态中加入置换环的个数的奇偶性，即可通过本题。

代码：[https://github.com/fei0319/competitive-programming/blob/main/code/luogu/P10547.cpp](https://github.com/fei0319/competitive-programming/blob/main/code/luogu/P10547.cpp)