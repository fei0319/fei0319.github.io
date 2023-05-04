---
title: 扩展欧几里得算法得出的解的绝对值上界
date: 2021-03-19T14:57:56+08:00
tags: ["数论", "扩展欧几里得"]
categories: ["数论"]
math: true
---

众所周知，扩展欧几里得算法是用于求解线性不定方程 $ax+by=(a,b)$ 的一组可行解的，其时间复杂度为对数级。

```cpp
void exgcd(int a, int b, int &x, int &y){
	if(!b) return (void)(x = 1, y = 0);
	exgcd(b, a % b, y, x), y -= a / b * x;
}
```

在计算过程中出现了乘法。$x$ 和 $y$ 是否有可能发生整型溢出呢？

事实上，除了 $b=0$ 的情况，在扩展欧几里得的过程中总是有 $|x|\le b,|y|\le a$，不可能发生溢出。这里给出简要证明：

记 $S=(a,b,x,y)$ 为一个同余方程 $ax+by=(a,b)$。中止状态解的同余方程为 $S_1=(\gcd(a,b),0,1,0)$。

考虑终止状态的前一个状态 $S_2=(a_2,b_2,x_2,y_2)$。此时一定有 $b_2\mid a_2$。根据公式

$$
\begin{cases}x_2=y_1\newline
y_2=x_1-\left\lfloor\dfrac{a}{b}\right\rfloor y_1\end{cases}
$$

解得 $x_2=0,y_2=1$。由于 $b_2\neq 0$ 且 $b_2\mid a_2$，则该状态满足 $|x_2|\le b_2,|y_2|\le a_2$。

归纳证明后面的情况。对于后面的任何一个状态 $S_i$ ($i>2$)，我们有 $x_i=y_{i-1},y_i=x_{i-1}-\left\lfloor\dfrac{a}{b}\right\rfloor y_{i-1}$。将 $|x_{i-1}|\le b_{i-1}=a_i\bmod b_i,|y_{i-1}|\le a_{i-1}=b_i$ 代入得到 $|x_i|\le b_i,|y_i|\le a_i$。
