---
title: 多项式的逆
date: 2021-01-13T22:13:23+08:00
tags: ["多项式", "高斯消元"]
categories: ["离散数学"]
math: true
---

数学中很多元素在一定条件下都存在逆元。多项式的逆元是什么呢？

从乘法逆元出发，一个简单的想法是，多项式 $g(x)$ 是 $f(x)$ 的逆元当且仅当 $f(x)\cdot g(x)=1$。遗憾的是，当且仅当 $f(x)$ 只含常数项时才存在满足条件的多项式 $g(x)$，否则 $g(x)$ 必须为无穷的幂级数。因此，我们在模的意义下考虑多项式的逆，得到多项式逆元的定义：

> $f(x)$ 的逆元 $g(x)$ 是一个满足 $f(x)\cdot g(x)\equiv 1\pmod{x^n}$ 的多项式。

分别用 $f_{0..n-1}$ 和 $g_{0..n-1}$ 来表示 $f(x)$ 和 $g(x)$ 的系数，把 $f(x)\cdot g(x)$ 写下来：

$$
f(x)\cdot g(x)\equiv \sum\limits_{i=0}^{n-1}\sum\limits_{j=0}^{i}f_{j}g_{i-j}\cdot x^{i}
$$

要满足上面的定义，$f(x)\cdot g(x)$ 就是 $x^0$ 项为 $1$，其余项为 $0$ 的多项式，即

$$
f_0g_0=1
$$

和

$$
\forall i\ge 1: \sum\limits_{j=0}^{i}f_{j}g_{i-j}=0
$$

这两个条件都成立，$g(x)$ 才是 $f(x)$ 的逆元。由第一个条件可知，若 $f(x)$ 的常数项为 $0$，则 $g(x)$ 一定不存在（$0$ 不存在逆元）。若 $f(x)$ 的常数项不为零，是否就一定存在满足第二个条件的 $g(x)$ 呢？

考虑把上面的式子视作一个 $n$ 元一次方程组，$f_{0..n-1}$ 是已知系数，$g_{0..n-1}$ 是未知数。由于我们有 $n$ 个关于 $n$ 个未知数的次数上升的方程，$g_{0..n-1}$ 一定有唯一解。

$$
\begin{cases}f_0g_0&=1\newline f_0g_1+f_1g_0&=0\newline f_0g_2+f_1g_1+f_2g_0&=0\newline f_0g_3+f_1g_2+f_2g_1+f_3g_0&=0\newline \ldots\newline f_0g_{n-1}+f_1g_{n-2}+f_2g_{n-3}+\ldots+f_{n-1}g_0&=0\end{cases}
$$

也就是说， **$f(x)$ 在模 $x^n$ 意义下存在逆元，当且仅当 $f(x)$ 的常数项非零；在 $f(x)$ 逆元存在的情况下，$f(x)$ 的逆元唯一**。

根据上述方程组求逆的时间复杂度是 $O(n^2)$，更快的 $O(n\log n)$ 算法请参考 [Newton's Method](/zh/posts/newtons-method)。
