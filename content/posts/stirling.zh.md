---
title: 斯特林数与上升幂、下降幂
date: 2021-01-28T14:29:04+08:00
tags: ["多项式", "斯特林数"]
categories: ["离散数学"]
math: true
---

> **本文内容已被整合至 OI Wiki，请至 [OI Wiki](https://oi-wiki.org/math/combinatorics/stirling/) 查看最新内容。**

斯特林数与二项式系数密切相关，是离散数学的重要内容。

斯特林数分为两类，分别是“第二类斯特林数”和“第一类斯特林数”。虽然被称作“第二类”，第二类斯特林数却比第一类的更常用，也在斯特林的相关著作和具体数学中被首先描述；因此，在这篇文章中我们也先介绍第二类斯特林数。

## 第二类斯特林数

第二类斯特林数又称作斯特林子集数，用 $\begin{Bmatrix}n\\m\end{Bmatrix}$ 表示，读作“$n$ 子集 $m$”。它的组合含义是，将 $n$ 个有标号物品划分为 $m$ 个无标号集合的方案数。学习[多项式对数函数、指数函数和欧拉变换](/2021/polylog)应该有助于能找到第二类斯特林数和指数函数的相关性（斯特林数限定了划分子集的个数而指数函数没有）。

可以认为，对于 $n \less m$，有 $\begin{Bmatrix}n\\m\end{Bmatrix}=0$。$m=0$ 的情况比较特殊。一般认为，将空集划分为 $0$ 个非空集合是可行的，因此我们定义 $\begin{Bmatrix}0\\0\end{Bmatrix}=1$；而对于任意 $n \greater 0$，都有 $\begin{Bmatrix}n\\0\end{Bmatrix}=0$。

和二项式系数类似，第二类斯特林数也有递推公式

$$
\begin{Bmatrix}n\\m\end{Bmatrix}=m\begin{Bmatrix}n-1\\m\end{Bmatrix}+\begin{Bmatrix}n-1\\m-1\end{Bmatrix},n>0
$$

同时，第二类斯特林数也有简洁的通项公式，即

$$
\begin{Bmatrix}n\\m\end{Bmatrix}=\sum\limits_{i=0}^m\dfrac{(-1)^{m-i}i^n}{i!(m-i)!}
$$

考虑使用容斥原理证明。设 $G_i$ 表示 $n$ 个有标号元素，放置到 $i$ 个有标号集合的方案数；$F_i$ 表示 $n$ 个有标号元素，放置到 $i$ 个有标号非空集合的方案数。不难得到

$$
G_i=i^n=\sum\limits_{j=0}^i\binom{i}{j}F_j
$$

根据二项式反演

$$
F_i=\sum\limits_{j=0}^{i}(-1)^{i-j}\binom{i}{j}j^n
$$

考虑第二类斯特林数与 $F_i$ 的关系

$$
\begin{Bmatrix}n\\i\end{Bmatrix}=\dfrac{F_i}{i!}
$$

联立得证。

## 同一行第二类斯特林数的计算

“同一行”的第二类斯特林数指的是，有着不同的 $i$，相同的 $n$ 的一系列 $\begin{Bmatrix}n\\i\end{Bmatrix}$。求出同一行的所有第二类斯特林数，就是对 $i=0..n$ 求出了将 $n$ 个不同元素划分为 $i$ 个非空集的方案数。

根据上面给出的通项公式，卷积计算即可。

```cpp
int main(){
	scanf("%d", &n);
	fact[0] = 1;
	for(int i = 1; i <= n; ++i) fact[i] = (ll)fact[i - 1] * i % mod;
	exgcd(fact[n], mod, ifact[n], ifact[0]), ifact[n] = (ifact[n] % mod + mod) % mod;
	for(int i = n - 1; i >= 0; --i) ifact[i] = (ll)ifact[i + 1] * (i + 1) % mod;
	fstdlib::poly f(n + 1), g(n + 1);
	for(int i = 0; i <= n; ++i) g[i] = (i & 1 ? mod - 1ll : 1ll) * ifact[i] % mod, f[i] = (ll)qpow(i, n) * ifact[i] % mod;
	f *= g, f.resize(n + 1);
	for(int i = 0; i <= n; ++i) printf("%d ", f[i]);
	return 0;
}
```

## 同一列第二类斯特林数的计算

即对 $i=0..n$，求出 $\begin{Bmatrix}i\\k\end{Bmatrix}$。有两种常用的快速计算方法。

### 方法 1. 利用递推公式

第二类斯特林数的通项公式不适合计算列，我们考虑利用递推公式写出它的生成函数。设 $F_k(x)=\sum\limits_{i=0}^n\begin{Bmatrix}i\\k\end{Bmatrix}x^i$，则

$$
F_k(x)=kxF_k(x)+xF_{k-1}(x)
$$

综合第二类斯特林数的定义解得

$$
F_k(x)=\begin{cases}\dfrac{x}{1-kx}F_{k-1}(x)&\text{if }k>0\\1&\text{else}\end{cases}
$$

即 $F_k(x)=\prod\limits_{i=1}^k\dfrac{x}{1-ix}$

利用多项式分治乘和多项式乘法逆即可在 $O(k\log k\log n)$ 的时间内解出 $F_k(x)$。

```cpp
int main(){
	scanf("%d%d", &n, &k);
	for(int i = 1; i <= k; ++i) mask.emplace_back(std::vector<int>{1, mod - i});
	while((int)mask.size() >= 2){
		while((int)mask.size() >= 2){
			tmp.push_back(mask[mask.size() - 1] * mask[mask.size() - 2]);
			mask.pop_back(), mask.pop_back();
		}
		if(!mask.empty()) tmp.push_back(mask[0]), mask.pop_back();
		std::swap(mask, tmp);
	}
	fstdlib::poly f(mask[0].inv(n + 1));
	for(int i = f.size() - 1; i >= k; --i) f[i] = f[i - k];
	for(int i = std::min(k, (int)f.size()) - 1; i >= 0; --i) f[i] = 0;
	for(int i = 0; i < (int)f.size(); ++i) printf("%d ", f[i]);
	return 0;
}
```

### 方法 2. 利用指数型生成函数

一个盒子装 $i$ 个物品的方案是 $\begin{cases}1&\text{if }i>0\\0&\text{else}\end{cases}$。我们可以写出它的指数型生成函数为 $F(x)=\sum\limits_{i=1}^{+\infty}\dfrac{x^i}{i!}$。经过之前的学习，我们明白 $F^k(x)$ 就是 $i$ 个有标号物品放到 $k$ 个有标号盒子里的指数型生成函数，$\exp F(x)=\sum\limits_{i=0}^{+\infty}\dfrac{F^i(x)}{i!}$ 就是 $i$ 个有标号物品放到任意多个无标号盒子里的指数型生成函数（指数函数通过每项除以一个 $i!$ 去掉了盒子的标号）。这里涉及到很多“有标号”“无标号”的内容，注意辨析。

那么 $\begin{Bmatrix}i\\k\end{Bmatrix}=\dfrac{\left[\dfrac{x^i}{i!}\right]F^k(x)}{k!}$，$O(n\log n)$ 计算多项式幂即可。实际使用时比 $O(n\log^2n)$ 的方法 1 要慢。

```cpp
int main(){
	scanf("%d%d", &n, &k);
	fstdlib::poly f(n + 1);
	fact[0] = 1;
	for(int i = 1; i <= n; ++i) fact[i] = (ll)fact[i - 1] * i % mod;
	for(int i = 1; i <= n; ++i) f[i] = qpow(fact[i], mod - 2);
	f = fstdlib::exp(fstdlib::log(f >> 1) * k) << k, f.resize(n + 1);
	int inv = qpow(fact[k], mod - 2);
	for(int i = 0; i <= n; ++i) printf("%lld ", (ll)f[i] * fact[i] % mod * inv % mod);
	return 0;
}
```

## 第一类斯特林数

第一类斯特林数又称作斯特林轮换数，用 $\begin{bmatrix}n\\m\end{bmatrix}$ 表示，读作“$n$ 轮换 $m$”。它的组合含义是，将 $n$ 个有标号物品分为 $m$ 个无标号轮换的方案数。

一个轮换就是一个首尾相接的环形排列。我们可以写出一个轮换$[A,B,C,D]$，并且我们认为 $[A,B,C,D]=[B,C,D,A]=[C,D,A,B]=[D,A,B,C]$，即，两个可以通过旋转而互相得到的轮换是等价的。注意，我们不认为两个可以通过翻转而相互得到的轮换等价，即 $[A,B,C,D]\neq[D,C,B,A]$。

不难发现，对于 $n>0$ 有 $\begin{bmatrix}n\\1\end{bmatrix}=(n-1)!$；进一步，也有 $\begin{bmatrix}n\\m\end{bmatrix}\geq \begin{Bmatrix}n\\m\end{Bmatrix}$。

轮换和排列一一对应，如果对同一行的所有第二类斯特林数求和，我们也能得到排列的总数 $\sum\limits_{i=0}^k\begin{bmatrix}n\\i\end{bmatrix}=n!$。这一点可以参考 _具体数学_。

类似第二类斯特林数，我们也可以写出第一类斯特林数的递推公式

$$
\begin{bmatrix}n\\m\end{bmatrix}=(n-1)\begin{bmatrix}n-1\\m\end{bmatrix}+\begin{bmatrix}n-1\\m-1\end{bmatrix},n>0
$$

## 同一行第一类斯特林数的计算

类似第二类斯特林数，我们构造同行第一类斯特林数的生成函数，即

$$
F_n(x)=\sum\limits_{i=0}^n\begin{bmatrix}n\\i\end{bmatrix}x^i
$$

根据递推公式，不难写出

$$
F_n(x)=(n-1)F_{n-1}(x)+xF_{n-1}(x)
$$

于是

$$
F_n(x)=\prod\limits_{i=0}^{n-1}(x+i)=\dfrac{(x+n-1)!}{(x-1)!}
$$

这其实是 $x$ 的 $n$ 次上升阶乘幂，记做 $x^{\overline n}$。这个东西自然是可以暴力分治乘 $O(n\log^2n)$ 求出的，但用上升幂相关做法可以 $O(n\log n)$ 求出。具体见下面有关阶乘幂的部分。

## 同一列第一类斯特林数的计算

仿照第二类斯特林数的计算，我们可以用指数型生成函数解决该问题。注意，由于递推公式和行有关，我们不能利用递推公式计算同列的第一类斯特林数。

显然，单个轮换的指数型生成函数为

$$
F(x)=\sum\limits_{i=1}^n\dfrac{(i-1)!x^i}{i!}=\sum\limits_{i=1}^n\dfrac{x^i}{i}
$$

它的 $k$ 次幂就是 $\begin{bmatrix}i\\k\end{bmatrix}$ 的指数型生成函数，$O(n\log n)$ 计算即可。

```cpp
int main(){
	scanf("%d%d", &n, &k);
	fact[0] = 1;
	for(int i = 1; i <= n; ++i) fact[i] = (ll)fact[i - 1] * i % mod;
	ifact[n] = qpow(fact[n], mod - 2);
	for(int i = n - 1; i >= 0; --i) ifact[i] = (ll)ifact[i + 1] * (i + 1) % mod;
	fstdlib::poly f(n + 1);
	for(int i = 1; i <= n; ++i) f[i] = (ll)fact[i - 1] * ifact[i] % mod;
	f = fstdlib::exp(fstdlib::log(f >> 1) * k) << k, f.resize(n + 1);
	for(int i = 0; i <= n; ++i) printf("%lld ", (ll)f[i] * fact[i] % mod * ifact[k] % mod);
	return 0;
}
```

## 上升阶乘幂和下降阶乘幂

之前我们提出了第一类斯特林数和上升阶乘幂的关系，即 $x$ 的 $n$ 次上升阶乘幂正是第 $n$ 行的第一类斯特林数的普通型生成函数。

$$
F_n(x)=x^{\overline n}
$$

接下来我们就介绍上升阶乘幂和与之类似的下降阶乘幂。

一般的，我们分别用 $x^{\overline n}$ 和 $x^{\underline n}$ 来表示 $x$ 的 $n$ 次上升阶乘幂和下降阶乘幂。它们可以被这样描述

$$
x^{\overline n}=\prod\limits_{i=0}^{n-1}(x+i)=\dfrac{(x+n-1)!}{(x-1)!},x^{\underline n}=\prod\limits_{i=0}^{n-1}(x-i)=\dfrac{x!}{(x-n)!}
$$

直观上看，上升幂和下降幂是对称的。我们可以写出

$$
\begin{aligned}(-x)^{\overline n}&=\prod\limits_{i=0}^{n-1}(-x+i)\\&=(-1)^n\prod_{i=0}^{n-1}(x-i)\\&=(-1)^nx^{\underline n}\end{aligned}
$$

同理，$(-x)^{\underline n}=(-1)^nx^{\overline n}$ 也是成立的。

我们还可以用下降阶乘幂表示二项式系数，这使得下降阶乘幂成为解决带组合数多项式的重要方法。

$$
\binom{n}{m}=\dfrac{n!}{(n-m)!m!}=\dfrac{n^{\underline m}}{m!}
$$

## 阶乘幂和两类斯特林数的关系

### 阶乘幂和第二类斯特林数的关系

我们先研究阶乘幂与第二类斯特林数的关系。事实上它们之间有这样的关系

$$
x^n=\sum\limits_{i=0}^n\begin{Bmatrix}n\\i\end{Bmatrix}x^{\underline{i}},n\geq 0
$$

我们用“生成函数”证明这一点。令

$$
F_n(x)=\sum\limits_{i=0}^n\begin{Bmatrix}n\\i\end{Bmatrix}x^{\underline i}
$$

根据第二类斯特林数的递推公式 $\begin{Bmatrix}n\\m\end{Bmatrix}=m\begin{Bmatrix}n-1\\m\end{Bmatrix}+\begin{Bmatrix}n-1\\m-1\end{Bmatrix}$ 可以写出

$$
\begin{aligned}F_n(x)&=\sum\limits_{i=0}^{n-1}i\begin{Bmatrix}n-1\\i\end{Bmatrix}x^{\underline i}+\sum\limits_{i=1}^{n}\begin{Bmatrix}n-1\\i-1\end{Bmatrix}x^{\underline {i}}\\&=\sum\limits_{i=0}^{n-1}i\begin{Bmatrix}n-1\\i\end{Bmatrix}x^{\underline i}+\sum\limits_{i=0}^{n-1}\begin{Bmatrix}n-1\\i\end{Bmatrix}x^{\underline {i+1}}\\&=\sum\limits_{i=0}^{n-1}\begin{Bmatrix}n-1\\i\end{Bmatrix}(i\cdot x^{\underline i}+x^{\underline {i+1}})\end{aligned}
$$

由 $x^{\underline {i+1}}=(x-i)\cdot x^{\underline{i}}$ 知

$$
\begin{aligned}i\cdot x^{\underline i}+x^{\underline {i+1}}&=i\cdot x^{\underline{i}}+(x-i)x^{\underline{i}}\\&=x \cdot x^{\underline i}\end{aligned}
$$

代入原式得

$$
\begin{aligned}F_n(x)&=\sum\limits_{i=0}^{n-1}\begin{Bmatrix}n-1\\i\end{Bmatrix}x\cdot x^{\underline i}\\&=x\cdot\sum\limits_{i=0}^{n-1}\begin{Bmatrix}n-1\\i\end{Bmatrix}x^{\underline i}\\&=x\cdot F_{n-1}(x)\end{aligned}
$$

由于 $F_0(x)=1$，因此 $F_n(x)=x^n$，得证。

当然，也可以根据上升、下降阶乘幂的关系，将其中的下降阶乘幂替换为上升阶乘幂

$$
(-x)^n=\sum\limits_{i=0}^n\begin{Bmatrix}n\\i\end{Bmatrix}(-1)^{i}x^{\overline i}
$$

也就是

$$
x^n=\sum\limits_{i=0}^n\begin{Bmatrix}n\\i\end{Bmatrix}(-1)^{n-i}x^{\overline i}
$$

第二类斯特林数建立了一般多项式向阶乘幂多项式转化的通道。它是一般多项式转上升、下降阶乘幂多项式的有力工具。

### 阶乘幂和第一类斯特林数的关系

通过之前构造的生成函数，我们已经知道

$$
x^{\overline n}=\sum\limits_{i=0}^n\begin{bmatrix}n\\i\end{bmatrix}x^i,n\ge 0
$$

同样，也可以写成下降幂的形式

$$
x^{\underline n}=\sum\limits_{i=0}^n\begin{bmatrix}n\\i\end{bmatrix}(-1)^{n-i}x^i
$$

第一类斯特林数和第二类斯特林数的作用正好相反，用于将上升、下降幂多项式转化为一般多项式。

## 下降阶乘幂在 OI 中的应用

上升阶乘幂在 OI 中的应用较少，在此不做介绍；我们只研究下降阶乘幂。

### 多项式下降阶乘幂表示与多项式点值表示的关系

在这里，多项式的下降阶乘幂表示就是用

$$
f(x)=\sum\limits_{i=0}^nb_i{x^\underline i}
$$

的形式表示一个多项式，而点值表示就是用 $n+1$ 个点

$$
(i,a_i),i=0..n
$$

来表示一个多项式。

显然，下降阶乘幂 $b$ 和点值 $a$ 间满足这样的关系：

$$
a_k=\sum\limits_{i=0}^{n}b_ik^{\underline i}
$$

即

$$
\begin{aligned}a_k&=\sum\limits_{i=0}^{n}\dfrac{b_ik!}{(k-i)!}\\\dfrac{a_k}{k!}&=\sum\limits_{i=0}^kb_i\dfrac{1}{(k-i)!}\end{aligned}
$$

这显然是个卷积形式，我们可以在 $O(n\log n)$ 的时间复杂度内完成点值和下降阶乘幂的互相转化。
