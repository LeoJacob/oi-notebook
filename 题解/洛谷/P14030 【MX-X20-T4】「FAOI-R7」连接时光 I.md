题目链接：[链接](https://www.luogu.com.cn/problem/P14030)

## 题意

小 T 有一个长度为 $n$ 的整数序列 $a_1, \ldots, a_n$（其中可能含有负数）。

对于一个 $1 \sim n$ 的排列 $p_1, \ldots, p_n$，小 T 会如下评估排列 $p$ 的价值 $f(p)$：

- 设置一张无向图 $G$，点的编号为 $1\sim n$，初始没有边。
- 对于所有 $1\le i<j\le n$ 且 $p_i>p_j$ 的对 $(i,j)$，在 $G$ 中添加一条连接 $(i,j)$ 且权值为 $a_j$ 的边。
- 如果 $G$ 不连通，则 $f(p)=-\infty$，否则 $f(p)$ 为 $G$ 中所有边的权值和。

你需要求出所有 $1 \sim n$ 的排列 $p$ 中 $f(p)$ 的最大值，并给出一个使 $f(p)$ 取到最大值的 $p$。

对于所有数据，$1\le n,T\le 10^5$，$1\le \sum n\le 2\times10^5$，$\lvert a_i\rvert\le10^9$。

## 题解

#### 目标分解

题目中需要满足两个目标：

1. 确保连出来的图连通；
2. 最大化边的权值和。

由于图连通是限制性条件，不是很好操作，因此先尝试排除这一条件，只看最大化，试图得到一些感觉。

#### 仅考虑最大化

根据题意，权值 $a_i$ 前的系数范围为 $[0, i)$，因此为了最大化权值，对于 $> 0$ 的部分，应该取到上限，而对于 $\le 0$ 的部分，应取到下限。

接着尝试构造，看看能否达到该界。容易得到如图构造：![[attachment/洛谷/P14030 【MX-X20-T4】「FAOI-R7」连接时光 I/P14030 【MX-X20-T4】「FAOI-R7」连接时光 I.excalidraw.md#^clippedframe=PHpxHG4S8vhbIySYA4mGD]]

上述构造中，满足 $a_i \le 0$ 的 $p_i$ 是 $p_1, p_2, \dots, p_i$ 中的最大值，$a_i > 0$ 的 $p_i$ 是 $p_1, p_2, \dots, p_i$ 中的最小值。

容易发现，如果以 $a_i > 0$ 的元素结尾，那么前面所有元素都会和这一个点相连。因此在仅考虑最大化的情况下，唯一可能出现不连通的情况是最后一个正数之后的若干负数。假设最后一个正数的位置是 $x$，如果想 $[1, x]$ 和 $[x + 1, n]$ 连通，那么必定有一条边是跨越这两个部分，并且代价是属于 $[x + 1, n]$ 的，与前面无关。

这提示我们先固定 $[1, x]$ 的最优解，然后缩成一个点，和后面的 $[x +1, n]$ 一起考虑，找到这一部分的最优解以后，再想办法和 $[1, x]$ 的最优解拼起来。

由于 $x$ 在 $[x, n]$ 中是第一个，不可能产生代价，故而寻找缩点后的最优解和寻找 $[x, n]$ 的最优解等价。

#### 寻找负数后缀最优解

现在目标是寻找 $[x, n]$ 的最优解。

仍然先在不考虑连通性的情况下尝试最大化。现在有 $n - x + 1$ 个点，至少要连 $n - x$ 条边才能连通。由于考虑的连边代价都是负数，因此当图成为一棵树时就不会再连边。问题转化为求剩下部分的最大生成树。这提示我们考虑 Kruskal 算法。由于 Kruskal 不好处理连边的 $p$ 之间的大小关系，因此先假定可以指定任意点对间边是否连边，求出一个比较紧的界以后再考虑构造。

根据 Kruskal 的执行过程，设 $\alpha$ 为 $\arg\max_{i \in [x + 1, n]} a_i$，则算法执行的最初阶段中，$a_x, a_{x + 1}, \dots, a_{\alpha - 1}$ 会连向 $a_\alpha$。然后，设 $a_\beta$ 为 $\arg \max_{i \in [\alpha + 1, n]} a_i$，则会有 $\beta - \alpha$ 条边连向 $a_\beta$，以此类推。最终结构如下图所示：![[attachment/洛谷/P14030 【MX-X20-T4】「FAOI-R7」连接时光 I/P14030 【MX-X20-T4】「FAOI-R7」连接时光 I.excalidraw.md#^clippedframe=YrewcDW-s_LQzMNvM8ipm|80%]]

由于 Kruskal 算法得到的最大值是一个限制更松的图论问题的上界，因此自然是原来问题的一个上界。现在尝试构造解。如果能通过构造达到的话，则说明达到了最大值。

可以看出图分成了一块一块的，而最后一块的不确定性最小，因此先看最后一块。容易得到最后一块应该是如下构造：![[attachment/洛谷/P14030 【MX-X20-T4】「FAOI-R7」连接时光 I/P14030 【MX-X20-T4】「FAOI-R7」连接时光 I.excalidraw.md#^clippedframe=3pJu8Xa7UB7TuJZTHeaDi]]

现在考虑两块的情况，假设块按顺序分别叫做 $A, B$。显然每块内的 $p$ 的相对大小关系都应该如上图。由于 $A$ 只能同 $B$ 连一条边，并且连到 $B$ 中后缀最小值上，因此可以得出，$A$ 中仅有最大的 $p$ 比 $B$ 中后缀最小值的 $p$ 大，但比其他的 $p$ 小；同时，$A$ 中其余的 $p$ 都比 $B$ 中后缀最小值的 $p$ 小。

多块的情况只需要不断往后添加块即可，分析情况类似。

容易想到以下构造：

1. 对于 $i \in [x + 1, n]$，初始令 $p_i = i$；
2. 对于每一块对应的区间，对其中的 $p$ 进行循环左移，以满足块内的大小关系；
3. 除第一块外，从左到右考虑每一块，把该块的 $p$ 最小值（即最后的元素），和前面所有块的 $p$ 最大值交换，即可合并两块。顺序做完每一块即可。

### 完整做法

完整做法大概可分为以下几步：

1. 计算答案
	1. 寻找正数的最后一次出现位置 $x$，并计算之前所有正数的贡献；
	2. 倒着从 $n$ 考虑到 $x$，计算后缀最小值，同时计算所有负数的贡献；
2. 构造排列
	1. $[1, x]$：用一个区间 $[l, r] \gets [1, x]$ 表示可用的值域，然后从后到前扫描，遇到正数分配当前可用的最小值，遇到负数分配最大值。
	2. $[x + 1, n]$：预先对于 $i \in [x + 1, n]$，令 $p_i \gets i$。从前到后按照 [寻找负数后缀最优解](#寻找负数后缀最优解) 中的做法合并即可。

实际实现时，为了求后缀最大值的方便，其实可以用等价的做法从后往前考虑，并且一个一个合并，而非一块一块合并。

- braveTester：做题时，实际上是根据直觉在尝试倒着构造，然后发现块内只有一个元素和大于等于两个的情况不一样，要分类讨论很多。观察两种情况突然灵光一现，发现是不是可以一个一个通过交换得到，从而想到的这个做法。

具体而言，最开始仍旧顺序分配 $p$。倒着做时，时刻保证后缀最小值分配的 $p$ 是当前后缀最小，然后仅需考虑当前元素的 $p$ 是 $i$，后缀最小值的 $p$ 是 $i + 1$，两者如何连边即可。连边这一步可以通过交换 $p$ 完成。

详细细节请参考代码。

## 代码

```c++
// Problem: P14030 【MX-X20-T4】「FAOI-R7」连接时光 I
// Contest: Luogu
// URL: https://www.luogu.com.cn/problem/P14030?contestId=275290
// Memory Limit: 512 MB
// Time Limit: 2000 ms
//
// Powered by CP Editor (https://cpeditor.org)

#include <algorithm>
#include <cstdio>
#include <cstdlib>

using namespace std;

const int NMAX = 210000;
const int INF = ~0u >> 1;

int T;
int N;
int A[NMAX];
int P[NMAX];

int main() {
    scanf("%d", &T);
    for (int t = 1; t <= T; t += 1) {
        scanf("%d", &N);
        for (int i = 1; i <= N; i += 1) {
            scanf("%d", &A[i]);
        }
        long long ans = 0;
        int last_pos_idx = -1;
        for (int i = 1; i <= N; i += 1) {
            if (A[i] > 0) {
                ans += 1ll * (i - 1) * A[i];
                last_pos_idx = i;
            }
        }
        int suf_max = -INF;
        for (int i = N; i > last_pos_idx && i >= 1; i -= 1) {
            if (A[i] > suf_max) {
                suf_max = A[i];
            }
            ans += suf_max;
        }
        if (last_pos_idx < 0) {
            ans -= suf_max;
        }
        printf("%lld\n", ans);
        int l = 1, r = last_pos_idx;
        int pre_idx = -1;
        for (int i = last_pos_idx; i >= 1; i -= 1) {
            if (A[i] > 0) {
                P[i] = l++;
            } else {
                P[i] = r--;
            }
            if (P[i] == last_pos_idx) {
                pre_idx = i;
            }
        }
        int suf_max_idx = N;
        for (int i = N; i > last_pos_idx && i >= 1; i -= 1) {
            P[i] = i;
            swap(P[i], P[suf_max_idx]);
            if (A[i] > A[suf_max_idx]) {
                suf_max_idx = i;
            }
        }
        if (pre_idx > 0 && last_pos_idx != N) {
            swap(P[pre_idx], P[suf_max_idx]);
        }
        for (int i = 1; i <= N; i += 1) {
            printf("%d%c", P[i], " \n"[i == N ? 1 : 0]);
        }
    }
    exit(0);
}
```