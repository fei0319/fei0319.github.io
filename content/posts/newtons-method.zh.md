---
title: 多项式中的 Newton's Method
date: 2021-01-14T21:00:43+08:00
tags: ["多项式", "牛顿迭代法"]
categories: ["离散数学"]
math: true
---

Newton's Method 是牛顿提出的一种将非线性方程线性化的近似方法。它也可以运用在多项式中，求关于多项式的非线性方程在模意义下的解。

要学习 Newton's Method，得先了解泰勒级数。

## 泰勒级数和麦克劳林级数

泰勒级数用无限项连加式来表示函数。一般地，对于一个光滑函数 $f(x)$，有

$$
f(x)=\sum\limits_{n=0}^{+\infty}\dfrac{f ^ {(n)}(a)} {i!}(x-a)^n
$$

这个等式被称为 $f(x)$ 在 $a$ 处的泰勒展开式；等号右边的式子被称为 $f(x)$ 在 $a$ 处的泰勒级数。在 $0$ 处的泰勒展开式和泰勒级数也被称为麦克劳林展开式和麦克劳林级数。下面给出了一些常用的麦克劳林级数，有的在生成函数中常用：

$$
\begin{aligned}
(1-x)^{-1}&=\sum\limits_{n=0}^{+\infty}x^n\newline
(1-x)^{-m}&=\sum\limits_{n=0}^{+\infty}\binom{n+m-1}{n}x^n\newline
(1+x)^{m}&=\sum\limits_{n=0}^{+\infty}\binom{m}{n}x^n\newline
\ln(1-x)&=-\sum\limits_{n=1}^{+\infty}\dfrac{x^n}{n}\newline
\ln(1+x)&=-\sum\limits_{n=1}^{+\infty}\dfrac{(-1)^{n}x^n}{n}\newline
\exp x&=-\sum\limits_{n=0}^{+\infty}\dfrac{x^n}{n!}
\end{aligned}
$$

> 泰勒展开式并非对于 $f(x)$ 定义域内所有 $x$ 都成立。比如，将 $x=10$ 代入 $(1-x)^{-1}=\sum\limits_{n=0}^{+\infty}x^n$，很容易发现这个式子根本不成立。事实上，$(1-x)^{-1}=\sum\limits_{n=0}^{+\infty}x^n$ 这个式子只对 $|x|<1$ 成立。
>
> 其他函数的泰勒展开式有的也有类似的限制。但在 OI 中我们只关心展开式的系数，不关心 $x$ 的值，可以认为这些泰勒展开式在我们研究的范围内总是成立的。

了解了泰勒级数，我们可以开始学习 Newton's Method 了：

## 一般的 Newton's Method

Newton's Method 一般被用于求解非线性方程。它是这样求 $f(x)=0$ 的根的：

- 选取合适一个数作为 $x_0$
- 将 $f(x)$ 在 $x_0$ 处展开，即 $f(x)=\sum\limits_{n=0}^{+\infty}\dfrac{f ^ {(n)}(x_0)} {n!}(x-x_0)^n$
- 取其常数项和线性项，令其值为 $0$，即 $f(x_0)+f^\prime(x_0)\cdot(x-x_0)=0$
- 解得这个近似方程的根 $x_1$，并在 $x_1$ 处将 $f(x)$ 泰勒展开，重复上述过程得到 $x_2,x_3,x_4,...$ 可以证明，每一个新解都更加接近 $f(x)=0$ 的根

```cpp
double f(double x);
double fd(double x);//fd 是 f 的导数，即 f'

double newtonsMethod(double x0, int d){ //d 代表迭代次数
	//f(x_0)+fd(x0)*(x-x0)=0 -> x=-f(x0)/fd(x0)+x0
    while(d--) x0 = -f(x0) / fd(x0) + x0;
    return x0;
}
```

这样看来，我们只能无限逼近 $f(x)=0$ 的根，而无法绝对准确地取到根。我们求多项式，是否也无法得到精确的解呢？担心是多余的，我们本来就不需要求“绝对精确”的解，只需要求模意义下的“精确”的解。

## Newton's Method 在多项式中的 Methodology

> 求一个关于 $x$ 的多项式 $f(x)$，使得对于一给定的关于 $f(x)$ 的函数 $g(f(x))$，$g(f(x))\equiv 0\pmod {x^n}$ 成立。

这是 Newton's Method 求解的问题的通式。对于多项式 $h(x)$，求逆时 $g(f(x))=h(x)-f^{-1}(x)$，开根时 $g(f(x))=h(x)-f^{2}(x)$，求指数时 $g(f(x))=h(x)-\ln f(x)$。下面就讲述 Newton's Method 是如何逼近根的：

选择一个满足 $g(f(x))\equiv0\pmod{x^1}$ 的多项式作为初始根。

设我们已经求出了模 $x^{\lceil\frac{n}{2}\rceil}$ 下的根 $f_0(x)$，将 $g(f(x))$ 在 $f_0(x)$ 处泰勒展开：

$$
g(f(x))\equiv\sum\limits_{i=0}^{+\infty}\dfrac{g ^ {(i)}(f_0(x))} {i!}(f(x)-f_0(x))^i\equiv 0\pmod{x^n}
$$

将 $f(x)=f_0(x)+x^{\lceil\frac{n}{2}\rceil}\cdot h(x)$ 代入

$$
\sum\limits_{i=0}^{+\infty}\dfrac{g ^ {(i)}(f_0(x))} {i!}(f_0(x)+x^{\lceil\frac{n}{2}\rceil}\cdot h(x)-f_0(x))^i\equiv 0\pmod{x^{n}}
$$

即

$$
\sum\limits_{i=0}^{+\infty}\dfrac{g ^ {(i)}(f_0(x))} {i!}\cdot x^{i\cdot\lceil\frac{n}{2}\rceil}\cdot h^i(x)\equiv 0\pmod{x^{n}}
$$

其中，对于 $x^{i\cdot\lceil\frac{n}{2}\rceil}\cdot h^i(x)$ 这一部分，显然当 $i\ge 2$ 时有 $x^{i\cdot\lceil\frac{n}{2}\rceil}\cdot h^i(x)\equiv 0\pmod{x^n}$。则在此处模 $x^n$ 意义下的泰勒级数，从第三项开始全部为 $0$，于是只需保留前两项：

$$
g(f_0(x))+g^\prime(f_0(x))\cdot (x^{\lceil\frac{n}{2}\rceil}\cdot h(x))\equiv 0\pmod{x^n}
$$

解得

$$
h(x)\equiv -x^{-\lceil\frac{n}{2}\rceil}\dfrac{g(f_0(x))}{g^\prime(f_0(x))}\pmod{x^n}
$$

在 $h(x)$ 存在的情况下回代得 $f(x)\equiv f_0(x)-\dfrac{g(f_0(x))}{g^\prime(f_0(x))}\pmod{x^n}$，完成了一次迭代。如果迭代 $a$ 次，我们就可以得到在模 $x^{2^a}$ 意义下的解了。

有一个问题尚未解决：$h(x)$ 是否总是存在？我们发现，$-x^{-\lceil\frac{n}{2}\rceil}\dfrac{g(f_0(x))}{g^\prime(f_0(x))}$ 在模 $x^n$ 意义下存在，当且仅当它只包含 $x$ 的自然数幂次项，而 $g(f_0(x))\equiv 0\pmod{x^{\lceil\frac{n}{2}\rceil}}$，于是在模 $x^n$ 意义下的 $\dfrac{g(f_0(x))}{g^\prime(f_0(x))}$ 总是存在，且其前 $\lceil\frac{n}{2}\rceil$ 总是为零。于是 $-x^{-\lceil\frac{n}{2}\rceil}\dfrac{g(f_0(x))}{g^\prime(f_0(x))}$ 总是一个只包含 $x$ 的自然数幂的式子，$h(x)$ 总是存在。

由上述过程可知，Newton's Method 可应用于解 $g(f(x))=0$ 模 $x^n$ 意义下的根，当且仅当 $g(f(x))$ 无限可微。$g(f(x))\equiv0\pmod{x^n}$ 的根存在，当且仅当这个同余方程在模 $x^1$ 意义下的根 $f_0$ 存在，并且每个 $f_0$ 都对应着唯一的一个模 $x^n$ 的根。除此之外，对于第 $a$ 次和第 $a+1$ 次迭代的根 $f_{a}(x),f_{a+1}(x)$，有 $f_a(x)\equiv f_{a+1}(x)\pmod{x^{2^a}}$。

下面将就几个具体的 $g(f(x))$ 讲解 Newton's Method 的应用。

## Newton's Method 求多项式的逆

对于原多项式 $h(x)$，它在模 $x^n$ 意义下的逆 $f(x)$ 满足 $h(x)\cdot g(x)\equiv0\pmod{x^n}$。则 $g(f(x))$ 可以写作 $g(f(x))=h(x)-\dfrac{1}{f(x)}$。代入 Newton's Method 得

$$
f(x)\equiv f_0(x)-\dfrac{h(x)-\dfrac{1}{f_0(x)}}{f_0^{-2}(x)}\pmod{x^n}
$$

注意这里 $g(f(x))$ 不是关于 $x$ 的复合函数，而是一个关于 $f(x)$ 的函数。它的导数要视作 $\dfrac{\mathrm{d}g(f(x))}{\mathrm{d}f(x)}$。

即

$$
f(x)\equiv f_0(x)\cdot\left(2-f_0(x)\cdot h(x)\right)\pmod{x^n}
$$

运用 FNTT，单次迭代复杂度为 $O(n\log n)$。运用主定理可得总时间复杂度为 $T(n)=T\left(\dfrac{n}{2}\right)+O(n\log n)=O(n\log n)$。我们一般用倍增法实现代码。

```cpp
void polyinv(int *f, const int *h, int n){
	int N = 1; while(N < n + n - 1) N <<= 1;
	static int d[maxn], g[maxn];
	memcpy(d, h, n * sizeof(int)), memset(d + n, 0, (N - n) * sizeof(int));
	memset(f, 0, N * sizeof(int)), memset(g, 0, N * sizeof(int)), f[0] = qpow(h[0], mod - 2);
	for(int w = 2; w / 2 < n; w <<= 1){
		memcpy(g, d, w * sizeof(int));
		for(int i = 0; i < w * 2; ++i) rev[i] = (rev[i >> 1] >> 1) | (i & 1 ? w : 0);
		dft(f, w << 1, 1), dft(g, w << 1, 1);
		for(int i = 0; i < w * 2; ++i) f[i] = (ll)f[i] * (2 - (ll)f[i] * g[i] % mod) % mod;
		dft(f, w << 1, -1);
		for(int i = 0, inv = qpow(w << 1, mod - 2); i < w; ++i) f[i] = (ll)f[i] * inv % mod;
		memset(f + w, 0, w * sizeof(int));
	}
	memset(f + n, 0, (N - n) * sizeof(int));
}
```

## Newton's Method 求多项式平方根

对于多项式 $h(x)$，它的平方根 $f(x)$ 满足 $f^2(x)\equiv h(x)\pmod{x^n}$，我们可以写出 $g(f(x))\equiv h(x)-f^2(x)\pmod{x^n}$。代入 Newton's Method 得

$$
\begin{aligned}
f(x)&\equiv f_0(x)-\dfrac{h(x)-f_0^2(x)}{-2f_0(x)}\newline
&\equiv f_0(x)+\dfrac{1}{2}\left(h(x)\cdot f_0^{-1}(x)-f_0(x)\right)\newline
&\equiv \dfrac{1}{2}\left(f_0(x)+f_0^{-1}(x)\cdot h(x)\right)
\end{aligned}\pmod{x^n}
$$

单次迭代仍为 $O(n\log n)$，故总复杂度也为 $O(n\log n)$。

```cpp
void polysqrt(int *f, const int *h, int n){
	int N = 1; while(N < n + n - 1) N <<= 1;
	static int d[maxn], g[maxn], f_inv[maxn];
	memcpy(d, h, n * sizeof(int)), memset(d + n, 0, (N - n) * sizeof(int));
	memset(f, 0, N * sizeof(int)), memset(g, 0, N * sizeof(int)), f[0] = 1;
	for(int w = 2; w / 2 < n; w <<= 1){
		memcpy(g, d, w * sizeof(int)), polyinv(f_inv, f, w);
		for(int i = 0; i < w * 2; ++i) rev[i] = (rev[i >> 1] >> 1) | (i & 1 ? w : 0);
		dft(g, w << 1, 1), dft(f, w << 1, 1), dft(f_inv, w << 1, 1);
		for(int i = 0; i < w * 2; ++i) f[i] = (f[i] + (ll)f_inv[i] * g[i] % mod) % mod;
		dft(f, w << 1, -1);
		for(int i = 0, inv = qpow(w << 2, mod - 2); i < w; ++i) f[i] = (ll)f[i] * inv % mod;
		memset(f + w, 0, w * sizeof(int));
	}
	memset(f + n, 0, (N - n) * sizeof(int));
}
```

## Newton's Method 求多项式指数函数

多项式的对数函数和指数函数都是用麦克劳林级数定义的。要用 Newton's Method 求多项式指数函数，得先会求[多项式对数函数](/zh/posts/polylog)。

对于多项式 $h(x)$，它的指数函数 $f(x)\equiv\exp h(x)\pmod{x^n}$ 满足 $h(x)\equiv\ln f(x)\pmod{x^n}$，我们可以写出 $g(f(x))=h(x)-\ln f(x)$。代入 Newton's Method 得

$$
\begin{aligned}
f(x)&\equiv f_0(x)-\dfrac{h(x)-\ln f_0(x)}{-\dfrac{1}{f_0(x)}}\newline
&\equiv f_0(x)+f_0(x)\cdot\left(h(x)-\ln f_0(x)\right)\newline
&\equiv f_0(x)\cdot(1+h(x)-\ln f_0(x))
\end{aligned}\pmod{x^n}
$$

单次迭代仍为 $O(n\log n)$，故总复杂度也为 $O(n\log n)$。

```cpp
void polyexp(int *f, const int *h, int n){
	int N = 1; while(N < n + n - 1) N <<= 1;
	static int d[maxn], g[maxn], lg[maxn]; memset(lg, 0, N * sizeof(int));
	memcpy(d, h, n * sizeof(int)), memset(d + n, 0, (N - n) * sizeof(int));
	memset(g, 0, N * sizeof(int)), memset(f, 0, N * sizeof(int)), f[0] = 1;
	for(int w = 2; w / 2 < n; w <<= 1){
		memcpy(g, d, w * sizeof(int)), polylog(lg, f, w);
		for(int i = 0; i < w * 2; ++i) rev[i] = (rev[i >> 1] >> 1) | (i & 1 ? w : 0);
		dft(g, w << 1, 1), dft(f, w << 1, 1), dft(lg, w << 1, 1);
		for(int i = 0; i < w * 2; ++i) f[i] = (ll)f[i] * (1 + g[i] - lg[i]) % mod;
		dft(f, w << 1, -1);
		for(int i = 0, inv = qpow(w << 1, mod - 2); i < w; ++i) f[i] = (ll)f[i] * inv % mod;
		memset(f + w, 0, w * sizeof(int));
	}
	memset(f + n, 0, (N - n) * sizeof(int));
}
```

一个有趣的事实是，要写多项式指数函数就必须先写一个多项式对数函数；要写多项式对数函数就必须先写一个多项式求逆......这些东西加起来有七八十行，还是不太好写的。
