---
title: "无损压缩算法基础及效率分析"
date: 2023-07-14T16:43:08+08:00
draft: false
math: true
tags: ["数据压缩"]
categories: ["数据处理"]
description: "香农-范诺编码、哈夫曼编码以及兰波-立夫-卫曲编码"
---

## 前缀码

在数据压缩时，我们为每个符号分配一个编码，实现源数据和压缩数据的互相转换。然而，在解压缩（Decompression）的过程中，对编码的不同划分方式会造成歧义。例如，对于如下编码表

|符号|编码|
|:-:|:-:|
|a|0|
|b|1|
|c|01|

编码串 01 既可解码为 ab 也可解码为 c。一种解决方法是人为增加分隔符，这样就要引入 0 和 1 之外的第三种编码符，需要占用约 $\log_2 3 \approx 1.58$ 倍于二进制编码的空间。于是我们寻求一种不需要分割符的编码方式。

如果任意一个编码都不是另一编码的前缀，则任意编码串都只有唯一的解码方式。具体地，对于下标从 $1$ 起计的编码串 $s$（$\Sigma=\\{0, 1\\}$），显然至少存在一个正整数 $k$ 使得 $s_{1\dots k}$ 是合法编码；若存在另一正整数 $k^\prime$ 使 $s_{1\dots k^\prime}$ 是合法编码，则某一编码是另一编码的前缀，与条件矛盾，故 $s$ 能且仅能在第 $1$ 个至 $k$ 个编码符间划分出第一个符号；归纳地推导，可知 $s$ 有唯一的解码方式。

这样互相不为前缀的编码系统被称为前缀码（Prefix Code）。为了不成为其他编码的前缀，某些编码不能够被使用，导致前缀码的编码平均长度大于朴素的二进制编码；但这种代价相比分隔符带来的代价是可接受的。

## 香农-范诺编码

香农-范诺编码（Shannon-Fano Coding）是一种为符号集构造前缀码的算法。在香农-范诺编码算法中，我们需要构造一棵有 $|\Sigma|$ 个叶子节点的树，每条指向左儿子的路径代表 0，指向右儿子的路径代表 1，从根节点到符号对应的叶子节点的路径上的数码构成了该符号的编码。显然这种构造方法能够保证前缀码互不为前缀的性质。

为了使最终编码的压缩数据最小，香农-范诺编码算法还要求我们统计每个符号在原数据中的出现频率 $f_c$。每次将当前节点的所有字符分为出现频率总和接近的两组，分别放入左儿子和右儿子；这样，出现频率较高的符号所在节点的深度“应该”会浅一些。

$$
\begin{array}{ll}
1 & \textbf{Input. }\text{Symbolset to be encoded } \Sigma \text{ where } |\Sigma|>1\newline
& \text{and symbols' frequencies in original data }f\newline
2 & \textbf{Output. } \text{A mapping from symbols in } \Sigma \text{ to binary codes}\newline
3 & \textbf{Method. }\newline
3 & \text{Creat an empty mapping }m\newline
5 & \textbf{Function}\text{ encode(}\Sigma, s\text{)}\newline
6 & \qquad \textbf{if } |\Sigma|=1\newline
7 & \qquad \qquad c \text{ is the only symbol in }\Sigma\newline
8 & \qquad \qquad m[c]\leftarrow s\newline
9 & \qquad \qquad \textbf{return}\newline
10 & \qquad \text{Divide } \Sigma \text{ into two parts } \Sigma_0, \Sigma_1 \text{ such that } \left|\sum\limits_{\Sigma_0}f-\sum\limits_{\Sigma_1}f\right| \text{ is minimized}\newline
11 & \qquad \text{encode(}\Sigma_0, \overline{s0}\text{)}\newline
12 & \qquad \text{encode(}\Sigma_1, \overline{s1}\text{)}\newline
13 & \qquad \textbf{return}\newline
14 & \textbf{End}\newline
15 & \text{encode(}\Sigma, \sigma\text{)}\newline
16 & \textbf{return }m
\end{array}
$$

## 哈夫曼编码

哈夫曼编码（Huffman Coding）是一种更为简单的编码算法。每次我们选择子树中总频率最小的两个节点，作为一个新节点的两个儿子；经过 $n-1$ 次合并后，得到一颗编码树。

哈夫曼编码构造的编码在所有的前缀单字符编码中具有最小的加权平均编码长度。注意到所有的编码树都可以通过这种自底到顶合并子树的方法构造，因此我们只需要证明哈夫曼编码给出了最优的合并方法。

**引理 1.** 对于两组数量相同的节点 $A,B$，若 $A$ 中除了两个节点外，其余节点均可以与 $B$ 中节点一一组成总频率相同的配对，设 $A,B$ 中两个未配对节点的总频率分别为 $a_1,a_2,b_1,b_2$，形式化地，

$$\begin{aligned}
f(A)=\\{x_1,x_2,\ldots,x_n,a_1,a_2\\},\newline
f(B)=\\{x_1,x_2,\ldots,x_n,b_1,b_2\\},\newline
a_1\le a_2, b_1\le b_2,\newline
a_1\neq b_1, a_2\neq b_2,\newline
\sum x_i + \sum a_i=\sum x_i + \sum b_i=1
\end{aligned}$$

若有 $a_1<b_1$ (同时 $a_2>b_2$)，则 $A$ 的最优的加权平均编码长度不大于 $B$ 的。

**证明 1.** 假定 $B$ 取得最优的加权平均编码长度时，其各节点对应的长度分别为 $l_1,l_2,\ldots,l_n,L_1,L_2$。易知 $L_1\le L_2$，否则 $l_1,l_2,\ldots,l_n,L_2,L_1$ 更优。令 $A$ 以相同方式编码，则 $A$ 的加权平均编码长度为

$$\sum x_il_i + a_1L_1 + a_2L_2$$

由 $a_1\le a_2, b_1 \le b_2, a_1+a_2=b_1+b_2, a_1<b_1, L_1\le L_2$ 可知 $a_1L_1+a_2L_2 \le b_1L_1 + b_2L_2$，即在该编码方式下$A$ 的加权平均编码长度不大于 $B$ 的最优的加权平均编码长度。

**引理 2.** 对于一系列总数多于 $3$ 的、总频率为 $f(A)=\\{a_1,a_2,\ldots,a_n\\}$（$a_1\le a_2\le\ldots\le a_n$）的节点 $A$，在其所有最优的编码方式中，至少有一种直接合并了 $a_1,a_2$。

**证明 2.** 通过以下三种合并方式，可以得到所有的不直接合并 $a_1, a_2$ 的编码方式：

**I.** 先合并 $a_1,a_i$，再合并 $a_2,a_j$（$i\neq j,i > 2,j>2$），再合并剩余节点

设在这种情况下得到了总频率为 $x_1,x_2,\ldots,x_{n-2},a_1+a_i,a_2+a_j$ 的一系列节点。若先合并 $a_1,a_2$，再合并 $a_i,a_j$，可以得到总频率为 $x_1,x_2,\ldots,x_{n-2},a_1+a_2,a_i+a_j$ 的一系列节点。两种方式的前两次合并都增加了 $a_1+a_2+a_i+a_j$ 的等量加权平均编码长度，由引理 1 可知在后续合并中后者得到的加权平均编码长度不多于前者；故后者是一种至少不劣于前者的合并方式。 

**II.** 先合并 $a_1,a_i$，再合并 $a_2,a_1+a_i$（$i>2$），再合并剩余节点

设在这种情况下得到了总频率为 $x_1,x_2,\ldots,x_{n-2},a_1+a_2+a_i$ 的一系列节点。若先合并 $a_1,a_2$，再合并 $a_1+a_2,a_i$，则同样可以得到总频率为 $x_1,x_2,\ldots,x_{n-2},a_1+a_2+a_i$ 的一系列节点，且在前两次合并中得到的加权平均编码长度 $a_1+a_2+a_1+a_2+a_i \le a_1+a_i+a_1+a_2+a_i$。

**III.** 先合并 $a_2,a_i$，再合并 $a_1,a_2+a_i$（$i>2$），再合并剩余节点

同 II，直接合并 $a_1,a_2$ 可以得到不大于 III 的加权平均编码长度。

即对于所有的不直接合并 $a_1, a_2$ 的编码方式，都存在至少一种直接合并 $a_1, a_2$ 的编码方式不劣于之，故在所有最优的编码方式中，至少有一种直接合并了 $a_1,a_2$。

有了引理 2，就可以用数学归纳法证明哈夫曼编码的最优性了。

## 兰波-立夫-卫曲编码