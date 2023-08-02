---
title: "柯尔莫哥洛夫复杂度"
date: 2023-07-16T23:42:10+08:00
draft: false
math: true
tags: ["可计算性", "渐进复杂度"]
categories: ["数据处理"]
---

柯尔莫哥洛夫复杂度（Kolmogorov Complexity）也称描述复杂度（Descriptive Complexity）、算法熵（Algorithmic Entropy），是一个衡量描述一个对象所需资源的量。具体地，对于一编程语言 $L$，对象 $s$ 的柯尔莫哥洛夫复杂度可记作 $K(s)$；它表示一个输出 $s$ 的 $L$ 程序的最小长度。

对于 $s=\text{hellohellohellohellohello}$，用 Python 可描述为 `'hello'*5`，并且我们假定这是最短的描述方法，那么对于 Python 来说 $K(s)=9$（或者，以二进制位数计的话，$K(s)=72$）。对于一些有规律或是重复性高的对象 $s$，$K(s)$ 相比 $|s|$ 大概会比较小；反之 $K(s)$ 大概会比较大。

我们注意到对于不同语言 $L$，同一对象的柯尔莫哥洛夫复杂度的复杂度是不同的。但这无关紧要，因为对于任意语言 $L, M$ 都存在一个仅由 $L,M$ 决定的常数 $C$ 使得 $\forall s, K_M(s) \le K_L(s) + C$：以 Python 和 C 为例，我们只要一个 CPython（以及一些其他的配套设施）的额外长度，就可以用 C 语言以 Python 的柯尔莫哥洛夫复杂度表示任何对象。由以上定理，任一对象在任何两个语言下的柯尔莫哥洛夫复杂度的差都是有确定上限的，因此对于足够大的对象来讲，区分不同语言下的柯尔莫哥洛夫复杂度是无意义的。

## 柯尔莫哥洛夫复杂度的不可计算性

似乎这样一个程序可以计算任一对象 $s$ 的柯尔莫哥洛夫复杂度：

$$
\begin{array}{ll}
1 & \textbf{Input. }\text{Object } s\newline
2 & \textbf{Output. } k\text{, length of the shortest program that yields }s\newline
3 & \textbf{Method. }\newline
4 & \textbf{Function}\text{ kolmogorov(}s{)}\newline
5 & \qquad \textbf{for }i\text{ from }1\text{ to }+\infty\newline
6 & \qquad \qquad \textbf{for }\text{each }p\text{ of length }i\newline
7 & \qquad \qquad \qquad \textbf{if }p\text{ is a valid program and eval}(p)=s\newline
8 & \qquad \qquad \qquad \qquad \textbf{return }p\newline
9 & \textbf{End}\newline
10 & \textbf{return }\text{kolmogorov(}s\text{)}\newline
\end{array}
$$

但这事实上并不可行，因为枚举的 $p$ 并不一定能在有限时间内结束，而停机问题（Halting Problem）的结论也告诉我们 $p$ 是否能在有限时间内结束是无法用算法确定的。

柯尔莫哥洛夫复杂度是不可计算的，哪怕是在理论上。假定存在一个程序 $K$ 可以计算任意对象的柯尔莫哥洛夫复杂度，那么这样一个程序 $P$ 可以输出一个柯尔莫哥洛夫复杂度不小于 $|K|+100000$ 的对象：

$$
\begin{array}{ll}
1 & \textbf{for }i\text{ from }1\text{ to }+\infty\newline
2 & \qquad \textbf{for }\text{each }s\text{ of length }i\newline
3 & \qquad \qquad \textbf{if }K(s)\ge |K|+100000\newline
4 & \qquad \qquad \qquad \textbf{return }s\newline
\end{array}
$$

然而该对象事实上可以被 $K$ 和 $P$ 输出，即其可以被一个长度为 $|K|+|P|$ 小于 $|K|+100000$ 的程序输出，和其柯尔莫哥洛夫复杂度不小于 $|K|+100000$ 的性质矛盾。