---
title: "TUCS：最终的比较排序法"
date: 2023-07-05T15:30:59+08:00
draft: false
math: true
tags: ["排序"]
categories: ["杂项"]
description: "基于比较的排序算法的复杂度的理论下界"
---

我们所知的所有基于比较的排序算法（Comparison Sorting Algorithm）都有 $\Omega(n\log n)$ 的最坏情况下的复杂度下界。仅有桶排序和基数排序等一类非比较排序算法可以得到更好的复杂度。

那么，是否存在一个 $o(n\log n)$ 的比较排序算法，只是我们没有发现呢？假定存在这样的算法，我们称之为 TUCS（the Ultimate Comparison Sort）。我们一眼就能看出，TUCS 的运行时间 $R(n)$ 满足 $R(n)\in O(n\log n), R(n) \in \Omega(n)$；上界由归并排序给出，下界则是由于序列中的每个元素都起码要被访问一次。

我们发现，当序列中元素均不相等时，它们可能的大小关系有 $n!$ 种。假定在第 $m$ 次比较前，我们已能确定序列元素的大小关系一定是这 $n!$ 种中的某 $p$ 种（记作 $C_m$）中的一个，那么在经过比较 $a_i < a_j$ 后，若结果为真，则我们可以进一步确认实际的大小关系是这 $p$ 种中的某 $q$ 种（记作 $C_q$）中的一个；若结果为非，则我们进一步确认实际的大小关系是这 $p$ 种中的某 $r$ 种（记作 $C_r$）中的一个。$C$ 中每一种大小关系都至少符合 $C_q$ 和 $C_r$ 中的一种，且只符合一种，故 $p=q+r$。

我们完全可以让每次比较后可能的大小关系的集合变为 $C_q$ 和 $C_r$ 中大小较大的那一个，即 $|C_{m+1}|=\max(q,r)\ge\dfrac{|C_m|}{2}$；在此种情况下，将可能的大小关系的集合的大小削减至 $1$ 所需的比较次数是 $\Theta(\log |C_0|)=\Theta(\log n!)$ 级别的。于是 $R(n) \in \Omega(\log n!)$。

接下来我们研究 $\Theta(\log n!)$ 是什么。由放缩 $n^n \le n! \le \left(\dfrac{n}{2}\right)^{n/2}$ 以及 $\log (n^n)\in \Theta(n\log n),\log\left(\dfrac{n}{2}\right)^{n/2}=\dfrac{n\log\dfrac{n}{2}}{2}\in \Theta(n\log n)$ 可知 $\log n!\in \Theta(n\log n)，即 $\Theta(\log n!)=\Theta(n\log n)$。

也就是说，TUCS 的复杂度就是 $\Theta(n\log n)$。世界上再没有最坏情况下复杂度更好的比较排序算法了！