---
title: 从概率生成函数出发研究离散概率
date: 2022-07-17T20:16:44+08:00
tags: ["概率论", "生成函数"]
categories: ["离散数学"]
math: true
description: "真的有必要绕这么大的弯吗？"
---

概率生成函数是一种特殊的生成函数，其导数和卷积在离散概率的研究中都有意义。

## 一些基础概念

对于一个仅取非负实数值的随机变量 $X$，我们定义其概率生成函数为

$$
G_X(z) = \sum_{k\ge 0} P(X=k)z^k
$$

与一般的幂级数形式的生成函数不同，概率生成函数存在非整数次的项。

## 导数、期望与方差

我们很少研究生成函数的导数，但概率生成函数的导数却值得研究：它与随机变量的期望和方差关系匪浅。由于

$$
G_X^\prime(z) = \sum_{k\ge 0} kP(X=k)z^{k-1}
$$

我们可以得到

$$
E(X) = \sum_{k\ge 0} kP(X=k) = G_X^\prime(1)
$$

同理我们也可以用 $G_X(z)$ 表示 $X$ 的方差 $V(X)$：

$$
\begin{aligned}
V(X) &= E(X^2) - E^2(X) \newline
&= E(X^2) - \left(G_X^\prime(1)\right)^2 \newline
&= G_X^{\prime\prime}(1) + G_X^\prime(1) - \left(G_X^\prime(1)\right)^2
\end{aligned}
$$

其中

$$
\begin{aligned}
E(X^2) &= \sum_{k\ge 0} k^2P(X=k) \newline
&= \sum_{k\ge 0}k(k-1)P(X=k) + \sum_{k\ge 0}kP(X=k) \newline
&= G_X^{\prime\prime}(1) + G_X^\prime(1)
\end{aligned}
$$

总而言之，我们有

$$
\begin{aligned}
E(X) &= G_X^\prime(1) \newline
V(X) &= G_X^{\prime\prime}(1) + G_X^\prime(1) - \left(G_X^\prime(1)\right)^2
\end{aligned}
$$

利用这个关系，我们可以方便地求出一些随机变量的期望、方差。以 $n$ 阶均匀分布为例 (即 $X$ 以 $\frac 1 n$ 的概率取遍 $[0,n-1]$ 范围内的每一个整数)：

$X$ 的概率生成函数为

$$
G_n(z) = \frac{1}{n}\sum_{k=0}^{n-1}z^k = \frac{1-z^n}{n(1-z)}
$$

研究一般的生成函数时，我们只关心各项系数而不关心生成函数在 $z$ 取特定值时的值，因而这种封闭形式的表达是没有问题的。但此时我们研究的是概率生成函数，我们需要知道 $z=1$ 时导数的值，而这种封闭形式的表达却要求 $z\neq 1$ 了。

一种思路是运用洛必达法则求 $\lim\limits_{z\rightarrow 1}G_n^\prime(z)$ 和 $\lim\limits_{z\rightarrow 1}G_n^{\prime\prime}(z)$，进而求出 $X$ 的期望、方差。这种方法十分困难，在此不做展开。

另一种思路是运用泰勒定理，得到

$$
G_n(1+z) = \sum_{k\ge 0} \frac{G^{(k)}(1)}{k!}z^k
$$

而由二项式定理

$$
G_n(1+z) = \frac{(1+z)^n-1}{nz} = \sum_{k\ge 0} \frac{1}{n} \binom{n}{k} z^k
$$

于是

$$
\frac{G^{(k)}(1)}{k!} = \frac{1}{n} \binom{n}{k}
$$

故

$$
G_n^\prime(1)=\frac{n-1}{2}, G_n^{\prime\prime}(1)=\frac{(n-1)(n-2)}{3}
$$

结合之前推导的关系，我们可以得出

$$
E(X)=\frac{n-1}{2}, V(X)=\frac{n^2-1}{12}
$$

类似地，我们也可以求泊松分布的期望、方差：

$$
G_\lambda(z) = \sum_{k \ge 0}\frac{e^{-\lambda}\lambda^k}{k!}z^k = e^{\lambda(z-1)}
$$

故

$$
G_\lambda(1+z) = e^{\lambda z} = \sum \frac{\lambda^k}{k!}z^k
$$

与泰勒展开式比较，可得 $G_\lambda^{(k)}=\lambda^k$，最终得到

$$
E(X)=\lambda, V(X)=\lambda
$$

然而，这种期望、方差的求法也不是万能的，将它用于二项分布就不是个好主意。

## 推导期望、方差的可加性

设 $X$ 和 $Y$ 为两相互独立的随机变量，则

$$
P(X = a \land Y = b) = P(X = a)P(Y = b)
$$

那么

$$
P(X + Y = n) = \sum_{a + b = n}P(X = a \land Y = b) = \sum_{a + b = n}P(X = a)P(Y = b)
$$

这是一个卷积形式的式子，可以完美地用生成函数表示。即，若 $X$ 和 $Y$ 相互独立，我们就可以得到

$$
G_{X+Y}(z) = G_X(z)G_Y(z)
$$

这个式子是我们利用概率生成函数推导期望、方差可加性的基石。设随机变量 $X$、$Y$ 和 $X+Y$ 的概率生成函数分别为 $F(z)$、$G(z)$ 和 $H(z)$，根据我们推导出的关系 $H(z)=F(z)G(z)$，可以得到

$$
\begin{aligned}
H^\prime(z) &= F^\prime(z)G(z) + F(z)G^\prime(z) \newline
H^{\prime\prime}(z) &= F^{\prime\prime}(z)G(z) + 2F^\prime(z)G^\prime(z) + F(z)G^{\prime\prime}(z)
\end{aligned}
$$

由概率生成函数的定义可知 $F(1)=G(1)=H(1)=1$，那么

$$
\begin{aligned}
H^\prime(1) &= F^\prime(1) + G^\prime(1) \newline
H^{\prime\prime}(1) &= F^{\prime\prime}(1) + 2F^\prime(1)G^\prime(1) + G^{\prime\prime}(1)
\end{aligned}
$$

代入公式

$$
\begin{aligned}
E(X) &= G_X^\prime(1) \newline
V(X) &= G_X^{\prime\prime}(1) + G_X^\prime(1) - \left(G_X^\prime(1)\right)^2
\end{aligned}
$$

可得

$$
\begin{aligned}
E(X+Y) &= E(X) + E(Y) \newline
V(X+Y) &= V(X) + V(Y)
\end{aligned}
$$

即期望和方差都具有可加性。

## 二项分布和负二项分布

二项分布我们都十分熟悉。由于它可以被看作 $n$ 个相互独立的伯努利试验的和，由可加性我们知它的期望和方差分别为 $np$ 和 $np(1-p)$。

而负二项分布是指：进行若干次相互独立的伯努利试验，成功次数为 $n$ 次时失败次数的概率分布。假定单次伯努利试验的成功概率为 $p$，那么成功一次时失败次数 $X$ 的概率分布显然满足

$$
P(X=k) = (1-p)^kp
$$

那么我们可以写出 $X$ 的概率生成函数为

$$
F(z) = \sum_{i \ge 0}(1-p)^ipz^i=\dfrac{p}{1-(1-p)z}
$$

且其期望和方差分别为

$$
E(X) = F^\prime(1) = \dfrac{(1-p)}{p}, V(X) = F^{\prime\prime}(1) + F^\prime(1) - \left(F^\prime(1)\right)^2 = \dfrac{(1-p)}{p^2}
$$

负二项分布进行了重复了上述过程 $n$ 次，且每次相互独立，则我们也可知 $X \sim NB(n, p)$ 的概率分布函数就是 $F(z)$ 的 $n$ 次方，即：

$$
G_n(z) = F^n(z) = \left(\dfrac{p}{1-(1-p)z}\right)^n=\sum\binom{n+i-1}{i}p^n(1-p)^iz^i
$$

当然它的期望和方差也可得分别为 $\dfrac{n(1-p)}{p}$ 和 $\dfrac{n(1-p)}{p^2}$。

负二项分布之名何来？我们发现，由于 $\dfrac{1}{F(1)} = 1$，$\dfrac{1}{F(z)}=\dfrac{1}{p}-\dfrac{1-p}{p}z$ 可被视做一次成功概率为 $-\dfrac{1-p}{p}$ （尽管它是个负数）的伯努利分布对应的概率分布函数，而 $G_n(z)=\left(\dfrac{1}{F(1)}\right)^{-n}$ 对应的就是 $-n$ （尽管这是个负数）次这样的伯努利试验的二项分布。

未完待续
