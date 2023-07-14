---
title: "无损压缩算法基础及效率分析"
date: 2023-07-14T16:43:08+08:00
draft: false
math: true
tags: ["数据压缩"]
categories: ["数据处理"]
description: "香农-范诺编码、哈夫曼编码以及蓝波-立夫-卫曲编码"
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

分析该算法编码的压缩数据的长度。