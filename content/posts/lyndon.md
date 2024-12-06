---
title: "Lyndon 串"
date: 2024-12-04T22:47:57+08:00
draft: false
math: true
tags: ["Lyndon串", "Lyndon分解", "Run"]
categories: ["字符串"]
---

我们称一个字符串 $s$ 是 Lyndon 串，当且仅当 $s$ 的字典序小于其所有的真后缀。由于 $s$ 和它的真后缀长度不同，此处的小于是严格小于。

## Lyndon 分解

任何字符串都可以分解为一系列 Lyndon 串 $w_1w_2\ldots w_k$（令 $w_i = s_i$ 即可得到一种分解），如果该分解还满足 $w_1 \ge w_2 \ge \ldots w_k$，则称该分解为 Lyndon 分解。任何字符串的 Lyndon 分解有且仅有一个。

- 存在性：首先将字符串分解为 $w_1w_2\ldots w_n, w_i = s_i$，其中 $w_i$ 均为 Lyndon 串。若相邻两项满足 $w_i < w_{i+1}$，则将它们拼接，显然拼接的结果也是 Lyndon 串。如此反复操作，直至相邻两项均不满足 $w_i < w_{i+1}$，即可得到一个 Lyndon 分解。
- 唯一性：假设存在两个 Lyndon 分解 $u_1 \ldots u_{p}$ 和 $v_1 \ldots v_{q}$。不失一般性，令 $u_1 \neq v_1$ 且 $|u_1| > |u_2|$，则有 $u_1 = v_1v_2 \ldots \overline{v_k}$，其中 $\overline{v_k}$ 是 $v_k$ 的一个前缀。由 $v_1 \ge v_2 \ge \ldots v_{k} \ge \overline{v_k}$ 可得 $v_1v_2 \ldots \overline{v_k} \ge \overline{v_k}$，这与 $u_1 < \overline{v_k}$ 矛盾。

## 最小表示

我们称 $s$ 的所有循环同构中字典序最小的为 $s$ 的最小表示。求 $ss$ 的 Lyndon 分解，取第一个右端点大于等于 $n$ 的字符串的首字符作为循环同构的起点，即可得最小表示。

> Lyndon 串是自己的最小表示，但字符串是自己的最小表示并不能说明其是 Lyndon 串。若 $s$ 为自己的最小表示且 $s$ 的最小正周期为 $|s|$，则 $s$ 为 Lyndon 串。

证明：对 $s^2$ 作 Lyndon 分解 $w_1w_2 \ldots w_k$，若 $w_1$ 为首个右端点大于等于 $n$ 的串，则由 Lyndon 串性质可知 $|w_1| = n$，即 $s$ 为 Lyndon 串；若 $w_x$（$x > 1$）为首个右端点大于等于 $n$ 的串，则 $s^2_{x\ldots x+n-1}$ 为 $s$ 的一个最小表示，又 $s$ 的最小周期为 $|s|$，$s$ 的循环同构两两不同，因此 $s$ 不是自己的最小表示，与题设矛盾。

## Run

我们称子串 $s_{l, r}$ 是 $s$ 的一个 Run，当且仅当：

- 令 $p$ 为 $s_{l, r}$ 的最小正周期，则 $r - l + 1 \ge p * 2$；
- $l = 1$ 或 $s_{l - 1} \neq s_{l - 1 + p}$；
- $r = n$ 或 $s_{r + 1} \neq s_{r + 1 - p}$。

简单地讲，Run 就是极长的周期子串，且周期至少完整出现两次。我们用有序三元组 $(i, j, p)$ 来表示一个 Run。

不妨设在 $(i, j, p)$ 所有长度为 $p$ 的子串中最小的那个为 $s_{l, r}$。那么 $s_{l, r}$ 必然是自己的一个最小表示，又因为 $s_{l, r}$ 的最小正周期为 $r - l + 1$，$s_{l, r}$ 为一个 Lyndon 串。我们称 $s_{l, r}$ 为 $(i, j, p)$ 的 Lyndon 根。如果能够枚举所有的 Lyndon 根，就可以通过哈希求往前后可拓展的长度，得到 Run。

考虑 $(i, j, p)$ 的一个 Lyndon 根 $s_{l, l + p - 1}$。比较 $s$ 的 $l$ - 后缀和 $(l + p)$ - 后缀的字典序，去除二者的 LCP，可知实际上就是比较 $s_{j + 1 - p}$ 和 $s_{j + 1}$。由 Run 的性质，必然有 $s_{j + 1 - p} \neq s_{j + 1}$。接下来讨论：

- 若 $s_{j + 1 - p} > s_{j + 1}$，设 $\text{rk} _i$ 表示 $i$ - 后缀字典序的排名，由 Lyndon 串的性质可知 $\forall l < x \le l + p - 1, \text{rk} _x > \text{rk} _l$ 而 $\text{rk} _{l + p} < \text{rk} _{l}$。于是若某个 Lyndon 根的开头为 $l$，我们可知它的结尾一定是最小的满足 $\text{rk} _r < \text{rk} _l$ 的 $r$。
- 若 $s_{j + 1 - p} < s_{j + 1}$，则我们找不到 $(i, j, p)$ 的 Lyndon 根。

对于每个 $l$ 找到第一个满足 $\text{rk}_r < \text{rk}_l$ 的 $r$，即可得到所有满足 $s _{j + 1 - p} < s _{j + 1}$ 的 $(i, j, p)$ 的 Lyndon 根。这个过程可以用单调栈和后缀数组在 $O(n)$ 内完成，也可以用单调栈和哈希在 $O(n\log n)$ 时间内完成。

> 事实上，对于 $l$，找出第一个满足 $\text{rk} _r < \text{rk} _l$ 的 $r$，则 $s _{l, r}$ 一定是 Lyndon 串，而且是以 $l$ 为开头的最长 Lyndon 串。不过这个知识对求 Run 无用。

对于那些 $s _{j + 1 - p} > s _{j + 1}$ 的 $(i, j, p)$，将 $s$ 的字符翻转后再应用上述过程即可得到它们的 Lyndon 根。因此我们最多可以得到 $2n$ 个可能的 Lyndon 根，即 Run 的总数不超过 $2n$。对这些 Lyndon 根进行扩展即可得到所有的 Run。

进一步地，Run 的总数小于 $n$，且 $\sum\limits_{(i, j, p)} \dfrac{j - i + 1}{p} < 3n$，证明略。

## 幂次串

我们称非空串 $s$ 是一个 $k$ 次串（$k > 1$），当且仅当存在一个 $t$ 满足 $s = t^k$。考虑字符串 $s$，对于它的一个 Run $(i, j, p)$，不难得到 $s_{l, l + kxp - 1}$（$x \ge 1, l \ge i, l + kxp \le j$）是一个 $k$ 次串。枚举所有 Run 得出所有该形式的 $k$ 次串，可以不重不漏地统计 $s$ 所有的 $k$ 次子串。

证明：若子串 $s_{l, r}$ 是一个 $k$ 次串，计算它的最小正周期 $p$，并向两侧扩展至不破坏该周期的极限，可以得到一个唯一的一个 Run，记该 Run 为 $R(l, r)$。若 $k$ 次子串 $s_{l, r}$ 会被 Run $(i, j, k)$ 计入，则有 $R(l, r) = (i, j, k)$，故 $s_{l, r}$ 会且仅会被 $R(l, r)$ 计入。
