---
title: "生成函数幂科技"
date: 2023-08-06T13:10:58+08:00
draft: false
math: true
tags: ["生成函数", "多项式"]
categories: ["离散数学"]
description: "一些神奇的科技"
---

给定 $F(z) = \sum\limits_{i=0}^n f_i x^i$，如何求出 $F^k \bmod x^m$？这样一个问题可以用快速幂在 $O(m\log m\log k)$ 的时间复杂度内解决，亦可以通过[多项式对数指数](/zh/posts/polylog/)在 $O(m \log m)$ 的时间复杂度内解决。但假定 $m$ 特别大，$n$ 特别小（$m=10^7,n=5$），那我们如何解决？

这就需要这样一个 $O(nm)$ 的生成函数幂科技了。令 $G=F^k$，我们有 $G^\prime=kF^\prime F^{k-1}$，故 $FG^\prime = kF^\prime G$。考虑 $x^i$ 项的系数，有

$$
\sum_{j=0}^{i+1} jf_{i-j+1} g_{j} = \sum_{j=0}^{i+1}k(i-j+1)f_{i-j+1} g_{j}
$$

根据 $g_{0}, g_{1}, \ldots, g_i$ 就可以 $O(m)$ 地计算 $g_{i+1}$ 了；而常数项 $g_0=f_0^k$。

这个方法不止适用于整数的 $k$，还适用于有理数的 $k$；不止适用于 OGF，还适用于 DGF。下面介绍一个计算 DGF 的 $k$ 次方根的例子。

给定 $F(z) = \sum\limits_{i=1}^n \dfrac{f_i}{i^z}$，求 $G$ 使得 $F=G^k$。不失一般性，我们只需求 $F^k$（$k$ 为有理数）；令 $G=F^k$，由上题结论可知 $FG^\prime = kF^\prime G$。考察 $F^\prime$，可知 $F^\prime = \sum\limits_{i=1}^n -\dfrac{\ln if_i}{i^z}$，故

$$
\sum_{d\mid i} \ln d f_{i/d}g_d = \sum_{d\mid i} k\ln\frac{i}{d} f_{i/d}g_d
$$

只要知道前面的 $g$，就可以 $O(\log n)$ 的平均复杂度求 $g_i$ 了。不过有一个问题仍待解决：在剩余系中 $\ln i$ 是什么？这甚至都不是离散对数，因为它的底不在剩余系中。为了规避这个问题，我们需要另一个 $\operatorname{t}$ 算子来取代微分算子。它需要具有某些和微分算子相同的特性，从而可以有 $F\operatorname{t}(G) = k\operatorname{t}(F) G$。

$F\operatorname{t}(G) = k\operatorname{t}(F) G$ 可以由 $\operatorname{d} uv = v\operatorname{d}u + u\operatorname{d}v$ 推出，故只要我们的 $\operatorname{t}$ 算子也满足 $\operatorname{t} uv = v\operatorname{t}u + u\operatorname{t}v$ 即可。如果我们规定 $\operatorname{t}(F)=\sum\limits_{i=1}^n \dfrac{\Omega(i)f_i}{i^z}$，其中 $\Omega(i)$ 为 $i$ 的质因子幂次和，那么我们就得到了一个满足条件的 $\operatorname{t}$ 算子，于是

$$
\sum_{d\mid i} \Omega(d) f_{i/d}g_d = \sum_{d\mid i} k\Omega\left(\frac{i}{d}\right) f_{i/d}g_d
$$

事实上任何满足 $p(xy) = p(x) + p(y)$ 且 $\forall x > 0, p(x) \neq 0$ 的数论函数都可用于取代 $\Omega$。

## Credits

*[A problem collection of ODE and differential technique](https://codeforces.com/blog/entry/76447)*, amiya