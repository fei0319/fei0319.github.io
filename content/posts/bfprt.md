---
title: "BFPRT：线性选择算法"
date: 2023-07-04T14:30:00+08:00
draft: false
math: true
tags: ["排序"]
categories: ["杂项"]
description: "共和国永远的主席、白头山绝世伟人、利比亚革命委员会主席兼武装部队总司令、可敬的父亲、祖国的恩人、全大陆民主领袖、自由斗士、全体工人保护者、国王中的国王、皇帝中的皇帝、中位数中的中位数"
---

选择算法（Selection Algorithm）是在一系列可比较元素中找到第 $k$ 小元素的算法。选择算法查找元素最典型的特例就是最大值和最小值，均可以 $O(n)$ 的时间复杂度完成。对于平凡的 $k$，显然也可通过排序在 $O(n \log n)$，或是堆在 $O(n \log k)$ 的时间复杂度内解决。本文章将介绍一个复杂度优于它们的选择算法，BFPRT 算法，其时间复杂度为 $O(n)$。

让我们先了解快速选择法（Quickselect），这是快速排序的发明者 Tony Hoare 发明的另一算法。它和快速排序唯一的区别就是其只递归第 $k$ 小值所在的一侧。在理想情况下，每次取到一个接近中位数的 Pivot，可以实现 $O(n)$ 的优秀复杂度；但在最坏情况下，复杂度可达 $O(n^2)$。

$$
\begin{array}{ll}
1 & \textbf{Input. }\text{1-indexed array of comparable elements } A_1, A_2, \ldots, A_n\newline
& \text{and positive integer } k \text{ indicating the index of the required element}\newline
2 & \textbf{Output. }k \text{-th smallest element of } A\newline
3 & \textbf{Method. }\newline
4 & \textbf{Function}\text{ quickselect(}A, k\text{)}\newline
5 & \qquad \text{select an integer } x \in [1, |A|]\newline
6 & \qquad \text{move all elements less than }x\text{ to the front}\newline
7 & \qquad \text{move all elements greater than }x\text{ to the back}\newline
8 & \qquad i \leftarrow \operatorname{rank}(A, x)\newline
9 & \qquad \textbf{if } k = i\newline
10 & \qquad \qquad \textbf{return } x\newline
11 & \qquad \textbf{else if } k < i\newline
12 & \qquad \qquad \textbf{return }\text{quickselect(}A[1...i-1], k\text{)}\newline
13 & \qquad \textbf{else}\newline
14 & \qquad \qquad \textbf{return }\text{quickselect(}A[i+1...|A|], k-i\text{)}\newline
15 & \textbf{End}\newline
16 & \textbf{return }\text{quickselect(}A,k\text{)}
\end{array}
$$

问题就在于我们如何选择 Pivot。我们当然可以随机地从序列中选取 Pivot，从而取得 $O(n)$ 的期望复杂度，但我们希望有一个确定性算法。一种朴素的想法是取头、中、尾三者的中位数作为 Pivot，但内省排序的发明者 David Musser 给出了数据使这种算法达到最坏复杂度。

包括我们熟悉的 Floyd 和 Tarjan 在内的姓氏缩写为 BFPRT 的五人给出一种选择 Pivot 的方法，使得快速选择的复杂度在最坏情况下达到 $O(n)$。

将原序列分为 $\left\lfloor\dfrac{n}{5}\right\rfloor$ 组，每组 $5$ 个元素（多余的元素舍去），暴力算出这 $\left\lfloor\dfrac{n}{5}\right\rfloor$ 组的中位数，记作 $a_1, a_2, \ldots, a_{\lfloor n/5 \rfloor}$。递归算出 $a$ 的中位数，作为 Pivot。

让我们来统计时间复杂度。暴力计算 $\left\lfloor\dfrac{n}{5}\right\rfloor$ 组中位数的复杂度为 $\sum O(5\log 5) = O(n)$，递归计算 $a$ 中位数的复杂度为 $T(\frac{n}{5})$，得到的 Pivot 至少大于等于 $\dfrac{n}{4}$、小于等于 $\dfrac{n}{4}$ 的数，故下一层递归至少排除了 $\dfrac{n}{4}$ 的数，复杂度为 $T(\frac{3n}{4})$。总复杂度由主定理可得为

$$\begin{aligned}
T(n) &= T\left(\frac{3}{4}n\\right) + T\left(\frac{n}{5}\right) + O(n)\newline
&= O(n)
\end{aligned}$$

为什么分每组 $5$ 个元素而不是 $4$ 个或 $6$ 个呢？记我们分每组 $a$ 个元素，由上面的式子可知 $a>4$。当 $a>4$ 时，比较次数 $f(n)$ 满足

$$
f(n)=
\begin{cases}
f\left(\left(\dfrac{3}{4} + \dfrac{1}{a}\right)n\right) + n \log a &\text{if } n \ge 5 \newline
n \log n &\text{if } n < 5
\end{cases}
$$

得 $f(n) = \dfrac{4na\log a}{a-4}$，由 $\dfrac{\partial f}{\partial a}=4n\dfrac{a-4\log a-4}{(4-a)^2}$ 发现 $f(n)$ 在 $n=15$ 处取得最小值。退一步讲，就算我们取暴力中位数的复杂度为 $O(na)$，那最优值也应是 $8$ 而非 $5$。

```cpp
#include <iostream>
#include <chrono>
#include <vector>
#include <numeric>
#include <cassert>
#include <random>

const int N = 1e6, K = N / 2;

std::vector<int> src(N);

int bruteforce_kth(std::vector<int> arr, int k) {
    std::sort(arr.begin(), arr.end());
    return arr[k - 1];
}

int get_kth(const std::vector<int> &, int, int);

int get_median(const std::vector<int> &arr, int a) {
    return get_kth(arr, arr.size() / 2 + 1, a);
}

int get_kth(const std::vector<int> &arr, int k, int a) {
    if (arr.size() <= a) {
        return bruteforce_kth(arr, k);
    }
    std::vector<int> medians;
    for (int i = 0; i + a - 1 < arr.size(); i += a) {
        std::vector<int> tmp(a);
        for (int j = 0; j < a; ++j) {
            tmp[j] = arr[i + j];
        }
        medians.push_back(bruteforce_kth(tmp, tmp.size() / 2 + 1));
    }
    int pivot = get_median(medians, a);
    std::vector<int> less, greater;
    for (int i : arr) {
        if (i < pivot) {
            less.push_back(i);
        } else if (i > pivot) {
            greater.push_back(i);
        }
    }
    if (k <= less.size()) {
        return get_kth(less, k, a);
    } else if (k <= arr.size() - greater.size()) {
        return pivot;
    } else {
        return get_kth(greater, k - (arr.size() - greater.size()), a);
    }
}

void run(int a) {
    auto start = std::chrono::high_resolution_clock::now();

    assert(get_kth(src, K, a) == K - 1);

    auto end = std::chrono::high_resolution_clock::now();
    std::chrono::duration<double> duration = end - start;
    std::cout << "a: " << a << " time: " << duration.count() << " seconds" << std::endl;
}

int main() {
    std::mt19937 rng(
        std::chrono::high_resolution_clock::now().time_since_epoch().count());
    std::iota(src.begin(), src.end(), 0);
    std::shuffle(src.begin(), src.end(), rng);
    
    for (int i = 5; i <= 2000; ++i)
        run(i);
    return 0;
}
```

在实际情况下存在各种其他因子的干扰，使得 $a$ 的最优值无法简单通过理论算出。通过运行以上代码，我们可以得到对于 $10^6$ 长度的随机序列求 $k$ 最小值的用时与 $a$ 的取值的关系图像。

![](/zh/images/bfprt1.png)
![](/zh/images/bfprt2.png)

似乎在 $100$ 到 $200$ 之间取得较优的效率。在其他机器上运行相同代码，最优值都不同，有在 $13$ 处取得最优值的。值得一提的是，以上实现即使在最优的 $a$ 取值下相比 $O(n\log n)$ 的 `std::sort` 仍然不具有显著优势。

上述代码本可以 in-place 分割从而得到更好的效率。也许我改天会重写并测试 in-place 版的 BFPRT 算法，或许能有新的发现。

## Credits

*[Linear Time Selection](https://www.cs.princeton.edu/~wayne/cs423/lectures/selection-4up.pdf)*, COS 423 Spring 2002, Kevin Wayne