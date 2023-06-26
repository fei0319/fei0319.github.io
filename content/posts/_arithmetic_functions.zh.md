---
title: 狄利克雷卷积和莫比乌斯反演
date: 2023-06-16T20:20:00+08:00
tags: ["多项式", "拉格朗日插值法"]
categories: ["离散数学"]
math: true
draft: false
---

数论函数是定义域为 $\mathbb{N}^{+}$，陪域为 $\mathbb{C}$ 的一类函数。常见的数论函数有：

- 单位函数 $\epsilon(n)=[n=1]$
- 欧拉函数 $\varphi(n)=\sum\limits_{i=1}^n[(i,n)=1]$
- 恒等函数 $\operatorname{id}(n)=n$
- 常值函数 $1(n)=1$
- 约数函数 $\tau(n)=\sum\limits_{i=1}^n[i\mid n]$ （亦可记作 $d(n)$、$\sigma_0(n)$）
- 莫比乌斯函数 $\mu(n)=\begin{cases}1&\text{if }n=1\newline 0&\text{if }p\text{ contains square prime divisor}\newline (-1)^{\omega(n)}&\text{otherwise}\end{cases}$，其中 $\omega(n)$ 代表 $n$ 的质因子个数。

若 $\forall x, y\in \mathbb{N}^{+}, (x, y)=1$ 都有 $f(xy)=f(x)f(y)$，则称数论函数 $f$ 为积性函数。若 $f$ 对 $\forall x, y\in \mathbb{N}^{+}, (x, y)\neq 1$ 也满足 $f(xy)=f(x)f(y)$ 则称 $f$ 为完全积性函数。上面给出的数论函数都是积性函数。

积性函数可以通过欧拉筛法线性求值，伪代码如下：

$$
\begin{array}{ll}
1 & \textbf{Input.}\text{ Positive integer }N\text{ and multiplicative function }f\newline
2 & \textbf{Output.}\text{ }f(n)\text{ for every positive integer }n\text{ that is less than or equal to } N\newline
3 & \textbf{Method.}\newline
4 & \textbf{Function}\text{ sieve(}N, f\text{)}\newline
5 & \qquad F\text{ is a 1-indexed array of length }N\newline
7 & \qquad\textbf{for}\text{ each integer }i\text{ in }[1, N]\newline
8 & \qquad\qquad\textbf{if }i\text{ is prime}\newline
9 & \qquad\qquad\qquad F_i \leftarrow f(i)\newline
10 & \qquad\qquad \textbf{for}\text{ each prime number }j\newline
11 & \qquad\qquad\qquad \textbf{if}\text{ }j\nmid i\newline
12 & \qquad\qquad\qquad\qquad F_{ij} \leftarrow F_iF_j\newline
13 & \qquad\qquad\qquad \textbf{else}\newline
14 & \qquad\qquad\qquad\qquad F_{ij} \leftarrow f(ij)\newline
15 & \qquad\qquad\qquad\qquad \textbf{break}\newline
16 & \qquad\textbf{return }F\newline
17 & \textbf{End}\newline
18 & \textbf{return }\text{sieve(}N,f\text{)}
\end{array}
$$

## 狄利克雷卷积

定义两个数论函数 $f$ 和 $g$ 的狄利克雷卷积 $f*g$ 为

$$(f*g)(n)=\sum\limits_{d\mid n}f(d)g\left(\dfrac{n}{d}\right)$$

事先了解生成函数（特别是狄利克雷型生成函数）有助于理解狄利克雷卷积。

狄利克雷卷积是离散卷积的一种，满足三种主要运算律：

- 交换律 $f\ast g=g\ast f$
- 结合律 $(f\ast g)\ast h=f\ast (g\ast h)$
- 分配律 $f\ast(g+h)=f\ast g+f\ast h$

正如 $1$ 是乘法的单位元（$x\times 1=x$），单位函数 $\epsilon$ 是狄利克雷卷积的单位元（$f*\epsilon=f$），任何数论函数 $f$ 与 $\epsilon$ 的狄利克雷卷积都是其本身。

## 莫比乌斯反演 · 其一

莫比乌斯反演有两个形式，在此先介绍其第一个形式，即

$$f(n)=\sum\limits_{d\mid n}g(n)\iff g(n)=\sum\limits_{d\mid n}\mu\left(\dfrac{n}{d}\right)f(d)$$

用狄利克雷卷积的形式表达就是

$$f=1*g\iff g=\mu * f$$

我们当即发现，莫比乌斯函数 $\mu$ 与单位函数 $1$ 之间似乎有非常紧密的关系，$\mu$ 似乎可以被视作是 $1$ 的逆元。换句话讲

$$1*\mu=\epsilon$$

$$\sum\limits_{d\mid n}\mu(d)=[n=1]$$

### 证明

由于含有平方质因子的数的莫比乌斯函数值为 $0$，设 $n=\prod {p_i}^{k_i},n^\prime=\prod p_i$，满足 $(1*\varphi)(n)=(1*\varphi)(n^\prime)$。

于是

$$(1*\varphi)(n)=(1*\varphi)(n^\prime)=\sum\binom{\omega(n^\prime)}{i}\cdot (-1)^i=0^{\omega(n^\prime)}$$

只有当 $n^\prime$ 的质因子数为 $0$，即 $n=n^\prime=1$ 时，才有 $(1*\varphi)(n)=1$。

当然，由于 $1*\varphi=\epsilon$ 和莫比乌斯反演的第一种形式是等价的，我们同时也证明了莫比乌斯反演的第一种形式。

## 莫比乌斯反演 · 其二

莫比乌斯反演的第二个形式是

$$
f(n)=\sum\limits_{n\mid d}g(d)\iff g(n)=\sum\limits_{n\mid d}\mu\left(\dfrac{d}{n}\right)f(d)
$$

我们发现它和第一个形式具有高度的对称性。接下来阐述如何在 $\sum\limits_{n=1}^\infty f(n)$ 和 $\sum\limits_{n=1}^\infty g(n)$ 均绝对收敛的条件下将第二种形式转化为第一种形式，从而证明之。

设 $N$ 为正整数， $M = N!$，且

$$
\begin{aligned}
F_N(x) = \begin{cases}f(n) &\text{ if }\exists n \in \mathbb N^+, n \le N, x = \dfrac{M}{n}\newline 0&\text{ else}\end{cases}\newline
G_N(x) = \begin{cases}g(n) &\text{ if }\exists n \in \mathbb N^+, n \le N, x = \dfrac{M}{n}\newline 0&\text{ else}\end{cases}
\end{aligned}
\qquad\left(n \le N\right)
$$

则对于任意 $N$ 和 $n \le N$ 有

$$
\begin{aligned}
f(n)=\sum\limits_{n\mid d}g(d)
&\iff F_N\left(\frac{M}{n}\right)-\sum\limits_{\frac{M}{d}\mid \frac{M}{n}}G_N\left(\frac{M}{d}\right) - \sum\limits_{n\mid d \land d>N}g(d) = 0\newline
&\iff F_N\left(n\right)-\sum\limits_{d\mid n}G_N\left(d\right) - \sum\limits_{n\mid d \land d>N}g(d) = 0
\end{aligned}
$$

其中余项满足 $\left|\sum\limits_{n\mid d \land d>N}g(d)\right| \le \sum\limits_{n\mid d \land d>N}|g(d)| \le \sum\limits_{n > N}|g(n)|$。由于 $\sum\limits_{n=1}^\infty g(n)$ 绝对收敛，则 $\forall \epsilon > 0, \exists N, \left|\sum\limits_{n\mid d \land d>N}g(d)\right| < \epsilon$。那么

$$
\begin{aligned}
f(n)=\sum\limits_{n\mid d}g(d)
&\iff
\lim_{N\to\infty} \left(F_N\left(n\right)-\sum\limits_{d\mid n}G_N\left(d\right) - \sum\limits_{n\mid d \land d>N}g(d)\right) = 0 \newline
&\iff
\lim_{N\to\infty} \left(F_N\left(n\right)-\sum\limits_{d\mid n}G_N\left(d\right)\right) = 0
\end{aligned}
$$

类似地，

$$
\begin{aligned}
g(n)=\sum\limits_{n\mid d}\mu\left(\dfrac{d}{n}\right)f(d)
&\iff G_N\left(\frac{M}{n}\right)-\sum\limits_{\frac{M}{d}\mid \frac{M}{n}}\mu\left(\frac{\frac{M}{n}}{\frac{M}{d}}\right)F_N\left(\frac{M}{d} \right) - \sum_{n\mid d \land d > N}\mu\left(\dfrac{d}{n}\right)f(d)=0\newline
&\iff G_N\left(n\right)-\sum\limits_{d\mid n}\mu\left(\frac{n}{d}\right)F_N\left(d \right) - \sum_{n\mid d \land d > N}\mu\left(\dfrac{d}{n}\right)f(d)=0
\end{aligned}
$$

由于 $|\mu(n)| \le 1$，余项 $\left|\sum\limits_{n\mid d \land d > N}\mu\left(\dfrac{d}{n}\right)f(d)\right| \le \sum\limits_{n\mid d \land d > N}|f(d)|\le \sum\limits_{n > N}|f(n)|$。由于 $\sum\limits_{n=1}^\infty f(n)$ 绝对收敛，类似上面的推理，可以得到

$$
g(n)=\sum\limits_{n\mid d}\mu\left(\dfrac{d}{n}\right)f(d)\iff
\lim_{N\to\infty}\left(G_N\left(n\right)-\sum\limits_{d\mid n}\mu\left(\frac{n}{d}\right)F_N\left(d \right)\right)=0
$$

由莫比乌斯反演的第一种形式，可以得到 $n\le N$ 时

$$
f(n)=\sum\limits_{n\mid d}g(d)\iff g(n)=\sum\limits_{n\mid d}\mu\left(\dfrac{d}{n}\right)f(d)
$$

由于 $N$ 可以取得任意大的值，上述结论可以推广至 $n \in \mathbb N^+$。

## 欧拉反演

$$n=\sum\limits_{d\mid n}\varphi(d)$$

用狄利克雷卷积的形式表达就是

$$\operatorname{id}=\varphi\ast 1$$
