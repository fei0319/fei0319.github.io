---
title: 多项式时间复杂度计算行列式
date: 2020-05-12T16:18:59+08:00
tags: ["矩阵", "行列式"]
categories: ["线性代数"]
math: true
---

矩阵的行列式在诸多领域有着广泛的应用。其最广为人知的应用便是在 Matrix-Tree 定理中求基尔霍夫矩阵的代数余子式。

根据行列式的定义，求其值的时间复杂度可达 $O(n\cdot n!)$。下面介绍一个能以多项式时间复杂度计算行列式的算法。

## 一些约定

- 用 $A_{i, j}$ 表示一个矩阵 $A$ 第 $i$ 行第 $j$ 列的值；
- 用 $|A|$ 表示关于 $n$ 阶方阵 $A$ 的行列式；

## 行列式的一般定义

$n$ 阶行列式是关于一个 $n$ 阶方阵的函数，写作 $|A|$ 或 $\text{det}(A)$。

设 $P$ 为 $1$ ~ $n$ 的一个排列，$\alpha(P)$ 为排列 $P$ 中的逆序对个数，有

$$
|A| = \sum\limits_{P \text{ 是 } 1 \text{ 至 } n \text{ 的一个排列 }}(-1)^{\alpha(P)}\cdot\prod_{i=1}^nA_{i, P_i}
$$

## 矩阵变换与行列式值的改变

由行列式的相关知识，我们必须要枚举所有的 $n!$ 种排列才能够得到行列式的值。为了改变我们的计算方法，就必须对矩阵进行一定的变换。下面是一些简单的引理：

> 引理 1. 交换矩阵的两行，行列式值变为原来的相反数

定义 $\omega(A, P) = (-1)^{\alpha(P)}\cdot\prod_{i=1}^nA_{i, P_i}$，那么 $|A| = \sum\limits_{P \text{ 是 } 1 \text{ 至 } n \text{ 的一个排列}}\omega(A, P)$。

交换矩阵的两行 $i, j$ 得到矩阵 $A'$。设同样交换 $P$ 的两个元素 $i, j$ 得到 $P'$，显然有 $\alpha(P') = \alpha(P) \pm 1$，即 $(-1) ^ {\alpha(P`)} = -(-1) ^ {\alpha(P)}$。

根据我们对 $A'$ 和 $P'$ 的定义，显然有 $\omega(A, P) = \omega(A', P')$。

那么

$$
\begin{aligned}|A'| &= -\sum\limits_{P \text{ 是 } 1 \text{ 至 } n \text{ 的一个排列}}\omega(A', P')\\ &= -\sum\limits_{P \text{ 是 } 1 \text{ 至 } n \text{ 的一个排列}}\omega(A, P)\\ &= -|A|\end{aligned}
$$

> 引理 2. 有两行完全相同的矩阵，其行列式为 $0$"

由**引理 1**可知，交换该矩阵的任意两行，其行列式变为相反数。若交换该矩阵相同的那两行，其矩阵不变，行列式变为相反数，即 $|A| = -|A|$。故 $|A| = 0$。

> 引理 3. 将矩阵的某一行乘以一个实数 $k$，其行列式变为原来的 $k$ 倍

> 引理 4. 若两矩阵除了一行之外完全相同，将两矩阵相加并减去相同部分得到矩阵的行列式为该两矩阵行列式之和

即

$$
\left|\begin{array}{ccc} A_{1,1} & \dots & A_{1,n-1} & A_{1,n}\newline
\cdots & \cdots & \cdots & \cdots\newline
X_{1}+Y_{1} & \dots & X_{n-1}+Y_{n-1} & X_{n}+Y_{n}\newline
\cdots & \cdots & \cdots & \cdots\newline
A_{n,1} & \cdots & A_{n,n-1} & A_{n,n}\end{array}\right|=
\left|\begin{array}{ccc} A_{1,1} & \dots & A_{1,n-1} & A_{1,n}\newline
\cdots & \cdots & \cdots & \cdots\newline
X_{1} & \dots & X_{n-1} & X_{n}\newline
\cdots & \cdots & \cdots & \cdots\newline
A_{n,1} & \cdots & A_{n,n-1} & A_{n,n}\end{array}\right|+\left|\begin{array}{ccc} A_{1,1} & \dots & A_{1,n-1} & A_{1,n}\newline
\cdots & \cdots & \cdots & \cdots\newline
Y_{1} & \dots & Y_{n-1} & Y_{n}\newline
\cdots & \cdots & \cdots & \cdots\newline
A_{n,1} & \cdots & A_{n,n-1} & A_{n,n}\end{array}\right|
$$

> 引理 5. 矩阵的某一行加上另一行的倍数，其行列式不变

即

$$
\left|\begin{array}{ccc} A_{1,1} & \dots & A_{1,n-1} & A_{1,n}\newline
\cdots & \cdots & \cdots & \cdots\newline
X_{1} & \dots & X_{n-1} & X_{n}\newline
Y_{1} & \dots & Y_{n-1} & Y_{n}\newline
\cdots & \cdots & \cdots & \cdots\newline
A_{n,1} & \cdots & A_{n,n-1} & A_{n,n}\end{array}\right|=\left|\begin{array}{ccc} A_{1,1} & \dots & A_{1,n-1} & A_{1,n}\newline
\cdots & \cdots & \cdots & \cdots\newline
X_{1}+k\cdot Y_{1} & \dots & X_{n-1}+k\cdot Y_{n-1} & X_{n}+k\cdot Y_{n}\newline
Y_{1} & \dots & Y_{n-1} & Y_{n}\newline
\cdots & \cdots & \cdots & \cdots\newline
A_{n,1} & \cdots & A_{n,n-1} & A_{n,n}\end{array}\right|
$$

这个结论可以引用**引理 2, 3, 4**证明：

$$
\begin{aligned}&\left|\begin{array}{ccc} A_{1,1} & \dots & A_{1,n-1} & A_{1,n}\newline
\cdots & \cdots & \cdots & \cdots\newline
X_{1}+k\cdot Y_{1} & \dots & X_{n-1}+k\cdot Y_{n-1} & X_{n}+k\cdot Y_{n}\newline
Y_{1} & \dots & Y_{n-1} & Y_{n}\newline
\cdots & \cdots & \cdots & \cdots\newline
A_{n,1} & \cdots & A_{n,n-1} & A_{n,n}\end{array}\right|\newline
&=\left|\begin{array}{ccc} A_{1,1} & \dots & A_{1,n-1} & A_{1,n}\newline
\cdots & \cdots & \cdots & \cdots\newline
X_{1} & \dots & X_{n-1} & X_{n}\newline
Y_{1} & \dots & Y_{n-1} & Y_{n}\newline
\cdots & \cdots & \cdots & \cdots\newline
A_{n,1} & \cdots & A_{n,n-1} & A_{n,n}\end{array}\right|+\left|\begin{array}{ccc} A_{1,1} & \dots & A_{1,n-1} & A_{1,n}\newline
\cdots & \cdots & \cdots & \cdots\newline
k\cdot Y_{1} & \dots & k\cdot Y_{n-1} & k\cdot Y_{n}\newline
Y_{1} & \dots & Y_{n-1} & Y_{n}\newline
\cdots & \cdots & \cdots & \cdots\newline
A_{n,1} & \cdots & A_{n,n-1} & A_{n,n}\end{array}\right|\newline
&=\left|\begin{array}{ccc} A_{1,1} & \dots & A_{1,n-1} & A_{1,n}\newline
\cdots & \cdots & \cdots & \cdots\newline
X_{1} & \dots & X_{n-1} & X_{n}\newline
Y_{1} & \dots & Y_{n-1} & Y_{n}\newline
\cdots & \cdots & \cdots & \cdots\newline
A_{n,1} & \cdots & A_{n,n-1} & A_{n,n}\end{array}\right|+k\cdot\left|\begin{array}{ccc} A_{1,1} & \dots & A_{1,n-1} & A_{1,n}\newline
\cdots & \cdots & \cdots & \cdots\newline
Y_{1} & \dots & Y_{n-1} & Y_{n}\newline
Y_{1} & \dots & Y_{n-1} & Y_{n}\newline
\cdots & \cdots & \cdots & \cdots\newline
A_{n,1} & \cdots & A_{n,n-1} & A_{n,n}\end{array}\right|\newline
&=\left|\begin{array}{ccc} A_{1,1} & \dots & A_{1,n-1} & A_{1,n}\newline
\cdots & \cdots & \cdots & \cdots\newline
X_{1} & \dots & X_{n-1} & X_{n}\newline
Y_{1} & \dots & Y_{n-1} & Y_{n}\newline
\cdots & \cdots & \cdots & \cdots\newline
A_{n,1} & \cdots & A_{n,n-1} & A_{n,n}\end{array}\right|\end{aligned}
$$

对于任意矩阵，交换其任意两行，其行列式变为相反数；对于任意矩阵，其中任意一行加上任意另一行的倍数，其行列式不变。

## 特殊矩阵的行列式

对于一个 $n$ 阶方阵，或是 $n$ 阶行列式，其从左上角至右下角连续 $n$ 个位置上的元素，称为它的主对角线。

对于一个方阵，若其主对角线以下的所有元素都为 $0$，则称之为上三角矩阵；若其主对角线以上的所有元素为 $0$，则称之为下三角矩阵。

由行列式的定义可知，若 $A$ 是上三角矩阵或下三角矩阵，$|A| = \prod\limits_{i=1}^nA_{i,i}$。

## 求行列式

只要运用**引理 1**和**引理 5**，用类似高斯消元的方法将矩阵转为上三角矩阵，我们就可以在 $O(n^3)$ 的时间内求出矩阵的行列式了。
