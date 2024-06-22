---
title: 任意模数多项式乘法
date: 2020-09-24T11:19:29+08:00
tags: ["多项式", "快速傅里叶变换"]
categories: ["离散数学"]
math: true
---

由于 NTT 的数学原理，它只能用于计算模特定模数意义下的多项式乘法。如何计算任意模数的多项式乘法呢？

一种简单的想法是利用中国剩余定理。一般我们计算两个长度在 $10^5$ 级别、系数在 $10^9$ 级别的多项式的卷积，其结果的系数不会超过 $10^{23}$ 级别。只要选取三个乘积超过 $10^{23}$ 的 NTT 模数，分别乘一次，再用中国剩余定理合并答案即可。这种方法常数极大。

而我们要介绍的做法是拆系数 FFT。合理的拆系数 FFT 实现消耗的时间仅仅是普通多项式乘法的四倍左右，是一种很优秀的任意模数多项式乘法计算方法。

## 拆系数 FFT

假定我们卷积的模数为 $p$，选取一个 $\sqrt{p}$ 级别的数 $d$。在 OI 中，$p$ 一般至多为 $2^{30}$ 级别的数。

把多项式 $F$ 每一项的系数 $F_i$ 分解为 $F_i=d\cdot G_i+H_i$，得到 $F=d\cdot G+H$。由于 $d$ 是 $\sqrt{p}$ 级别的，我们可以保证 $G$ 和 $H$ 的系数也是 $\sqrt{p}$ 级别的，相乘不会超过 `double` 的精度。

于是

$$
\begin{aligned}F\cdot G&=\left(d\cdot A+B\right)\cdot\left(d\cdot C+D\right)\newline &=d^2\cdot AC+d\cdot(AD+BC)+BD\end{aligned}
$$

使用“三次变两次”的思想优化，可以在七次 DFT 变换内得到结果 $F\cdot G$。这就是拆系数 FFT 了。

## 优化

根据国家集训队论文《再谈快速傅里叶变换》，存在一种四次 DFT 计算任意模数卷积的算法。

设 $F=G+i\cdot H$，对于 FFT 选取的每一个点 $\left(\omega_n^k,F\left(\omega_n^k\right)\right)$，都有

$$
\begin{aligned}F\left(\omega_n^k\right)&= G\left(\omega_n^k\right)+i\cdot H\left(\omega_n^k\right)\newline
&=\sum\limits_{j=0}^{n-1}\left(G_j\cdot\omega_n^{jk}+i\cdot H_j\cdot\omega_n^{jk}\right)\newline
&=\sum\limits_{j=0}^{n-1}\omega_{n}^{jk}\cdot\left(G_j+i\cdot H_j\right)\newline
&=\sum\limits_{j=0}^{n-1}\left(\cos\dfrac{kj\cdot 2\pi}{n}+i\cdot\sin\dfrac{kj\cdot 2\pi}{n}\right)\cdot\left(G_j+i\cdot H_j\right)\end{aligned}
$$

再设 $\overline{F}=G-i\cdot H$，我们有

$$
\begin{aligned}\overline{F}\left(\omega_n^k\right)&=\sum\limits_{j=0}^{n-1}\left(\cos\dfrac{kj\cdot 2\pi}{n}+i\cdot\sin\dfrac{kj\cdot 2\pi}{n}\right)\cdot\left(G_j-i\cdot H_j\right)\newline
&=\sum\limits_{j=0}^{n-1}\left(\left(G_j\cdot\cos\dfrac{kj\cdot 2\pi}{n}+H_j\cdot\sin\dfrac{kj\cdot 2\pi}{n}\right)+i\cdot\left(G_j\cdot\sin\dfrac{kj\cdot 2\pi}{n}-H_j\cdot\cos\dfrac{kj\cdot 2\pi}{n}\right)\right)\newline
&=\sum\limits_{j=0}^{n-1}\left(\left(G_j\cdot\cos\left(-\dfrac{kj\cdot 2\pi}{n}\right)-H_j\cdot\sin\left(-\dfrac{kj\cdot 2\pi}{n}\right)\right)-i\cdot\left(G_j\cdot\sin\left(-\dfrac{kj\cdot 2\pi}{n}\right)+H_j\cdot\cos\left(-\dfrac{kj\cdot 2\pi}{n}\right)\right)\right)\newline
&=\sum\limits_{j=0}^{n-1}\left(\left(G_j\cdot\cos\dfrac{(nj-kj)\cdot 2\pi}{n}-H_j\cdot\sin\dfrac{(nj-kj)\cdot 2\pi}{n}\right)-i\cdot\left(G_j\cdot\sin\dfrac{(nj-kj)\cdot 2\pi}{n}+H_j\cdot\cos\dfrac{(nj-kj)\cdot 2\pi}{n}\right)\right)\newline
&=\overline{\sum\limits_{j=0}^{n-1}\left(\left(G_j\cdot\cos\dfrac{(nj-kj)\cdot 2\pi}{n}-H_j\cdot\sin\dfrac{(nj-kj)\cdot 2\pi}{n}\right)+i\cdot\left(G_j\cdot\sin\dfrac{(nj-kj)\cdot 2\pi}{n}+H_j\cdot\cos\dfrac{(nj-kj)\cdot 2\pi}{n}\right)\right)}\newline
&=\overline{\sum\limits_{j=0}^{n-1}\left(\cos\dfrac{(nj-kj)\cdot 2\pi}{n}+i\cdot\sin\dfrac{(nj-kj)\cdot 2\pi}{n}\right)\cdot\left(G_j+i\cdot H_j\right)}\\&=\overline{G\left(\omega_n^{n-k}\right)+i\cdot H\left(\omega_n^{n-k}\right)}\newline
&=\overline{F\left(\omega_n^{n-k}\right)}\end{aligned}
$$

因此，只要求出 $F$ 的点值，就可 $O(n)$ 地推出 $\overline{F}$ 的点值，解出 $G,H$ 的点值。即

$$
\begin{aligned}
\text{DFT}(G)&=\dfrac{\text{DFT}(F)+\text{DFT}(\overline{F})}{2}\newline
\text{DFT}(H)&=\dfrac{\text{DFT}(F)-\text{DFT}(\overline{F})}{2i}
\end{aligned}
$$

这样，一共只需要两次 DFT 就可以得到四个多项式的点值。

还原系数表达（IDFT）时，也可以用“三次变两次”的思想，将两个多项式分虚实放在一起 IDFT，做到两次 IDFT 还原。

```cpp
#include <cstdio>
#include <cmath>

#include <vector>
#include <complex>

#define double long double

const double PI = std::acos(-1);

namespace __fft{
	typedef std::complex<double> comp;

	std::vector<comp> tmp;
	std::vector<int> rev;

	void init(int n){
		tmp.resize(n), rev.resize(n);
		for(int i = 0; i < n; ++i){
			rev[i] = rev[i >> 1] >> 1;
			if(i & 1) rev[i] |= n >> 1;
		}
	}

	void fft(std::vector<comp>::iterator f, int n, int b){
		for(int i = 0; i < n; ++i)
			if(i < rev[i]) std::swap(f[i], f[rev[i]]);
		for(int i = 2; i <= n; i <<= 1){
			comp w(std::cos(2 * PI / i), b * std::sin(2 * PI / i));
			for(int j = 0; j < n; j += i){
				comp wn(1, 0);
				std::vector<comp>::iterator g = f + j, h = f + j + i / 2;
				for(int k = 0; k < i / 2; ++k){
					tmp[k] = g[k] + h[k] * wn;
					tmp[k + i / 2] = g[k] - h[k] * wn;
					wn = wn * w;
				}
				for(int k = 0; k < i; ++k) f[j + k] = tmp[k];
			}
		}
	}
}

int n, m, p;
std::vector<int> F, G;

std::vector<int> mtt(std::vector<int> F, std::vector<int> G, int len){
	typedef long long ll;
	using __fft::comp;
	__fft::init(len);

	int d = 1 << 15;

	std::vector<comp> A(len), B(len);
	std::vector<comp> C(len), D(len);
	for(int i = 0; i < len; ++i)
		A[i] = comp(F[i] / d, 0), B[i] = comp(F[i] % d, 0);
	for(int i = 0; i < len; ++i)
		C[i] = comp(G[i] / d, 0), D[i] = comp(G[i] % d, 0);

	std::vector<comp> H(len), I(len);

	for(int i = 0; i < len; ++i)
		H[i] = comp(A[i].real(), B[i].real());
	__fft::fft(H.begin(), len, 1);
	for(int i = 0; i < len; ++i)
		I[i] = conj(H[(len - i) % len]);
	for(int i = 0; i < len; ++i){
		A[i] = (H[i] + I[i]) * comp(0.5, 0);
		B[i] = (H[i] - I[i]) * comp(0, -0.5);
	}

	for(int i = 0; i < len; ++i)
		H[i] = comp(C[i].real(), D[i].real());
	__fft::fft(H.begin(), len, 1);
	for(int i = 0; i < len; ++i)
		I[i] = conj(H[(len - i) % len]);
	for(int i = 0; i < len; ++i){
		C[i] = (H[i] + I[i]) * comp(0.5, 0);
		D[i] = (H[i] - I[i]) * comp(0, -0.5);
	}

	std::vector<comp> P(len), Q(len);
	for(int i = 0; i < len; ++i){
		P[i] = A[i] * C[i] + comp(0, 1) * (A[i] * D[i] + B[i] * C[i]);
		Q[i] = B[i] * D[i];
	}
	__fft::fft(P.begin(), len, -1), __fft::fft(Q.begin(), len, -1);

	for(int i = 0; i < len; ++i){
		F[i] = (ll)(P[i].real() / len + 0.9) * d % p * d % p;
		F[i] = (F[i] + (ll)(P[i].imag() / len + 0.9) * d) % p;
		F[i] = (F[i] + (ll)(Q[i].real() / len + 0.9)) % p;
		F[i] = (F[i] + p) % p;
	}
	return F;
}

int main(){
	std::scanf("%d%d%d", &n, &m, &p);
	F.resize(n + 1), G.resize(m + 1);
	for(int i = 0; i <= n; ++i) std::scanf("%d", &F[i]), F[i] %= p;
	for(int i = 0; i <= m; ++i) std::scanf("%d", &G[i]), G[i] %= p;

	int len = 0;
	for(int i = 0; i <= 20; ++i)
		if((1 << i) >= n + m + 1){
			len = 1 << i; break;
		}
	F.resize(len), G.resize(len);

	std::vector<int> ans(mtt(F, G, len));
	for(int i = 0; i <= n + m; ++i) std::printf("%d ", ans[i]);
	return 0;
}
```

以上是我早年间的实现，效率极其低下（$10^5$ 卷积运算耗时 $1109$ ms）。更好的实现可以参考我的项目 [fstdlib](https://github.com/fei0319/poly.h)。

拆系数 FFT 是无法通过 [Library Checker](https://judge.yosupo.jp/problem/convolution_mod_1000000007) 的，因为 Library Checker 的多项式长度达到了 $524288$，纵使是 `long double` 也无法保证精度。正常情况下不会有题目考察规模如此之大的任意模数卷积。
