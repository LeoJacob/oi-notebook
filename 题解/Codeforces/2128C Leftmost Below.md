---
tags:
  - Codeforces
  - 贪心
  - 简单到复杂
---

题目链接：[链接](https://codeforces.com/problemset/problem/2128/C)

## 题意

考虑一个数组 $a_1, a_2, \dots, a_n$。初始时，每个元素 $a_i = 0$。

可以执行以下操作任意多次：

- 选择一个整数 $x$，满足 $x > \min(a)$。
- 然后，找到最小的下标 $i$，使得 $a_i < x$ 且对于所有 $1 \leq j \leq i-1$，有 $a_j \geq x$。
- 最后，将 $a_i$ 增加 $x$。

问：是否可以通过一系列操作，使数组变为目标数组 $b_1, b_2, \dots, b_n$？

## 题解

首先尝试分析一下题目条件。

根据题目条件容易得到，能操作的位置恰为前缀最小值。例如，对于 $a = [5, 3, 4, 1, 2, 1]$，能操作的位置为 $a = [\textcolor{red}{5}, \textcolor{red}{3}, 4, \textcolor{red}{1}, 2]$。

一个能操作的位置被操作了以后，显然不会改变前面能操作的位置。由于是让操作数变大，因此后面能操作的位置仍然是可以操作的。此外，由于该位置上的值变大，可能会让下一个能操作位置之前的若干个数从不是前缀最小值变为前缀最小值，继而可以操作。

从题目条件直接分析能得到的信息似乎就这么多，然而和目标 $b$ 的关系仍不清晰，不知道如何下手。

此时可以尝试人为简化问题，从简单情况入手进行思考。

首先考虑只有一个元素的情况。容易发现，此时无论 $b$ 是何值，都可以通过一次操作完成。

现在考虑两个元素的情况。容易发现，$a_1$ 的值会影响 $a_2$ 能使用的 $x$ 的范围，并且 $a_1$ 越大，$a_2$ 能使用的 $x$ 越多。因此 $a_1$ 越大不会更差。所以，假设最终可以操作成功，$a_1$ 总是会变成 $b_1$，以使得 $a_2$ 能用的 $x$ 最多，为 $[1, b_1]$。

下面考虑如果可以成功的话，$b_2$ 应该满足什么样的条件。由于 $a_2$ 能使用的 $x$ 受到 $b_1$ 的限制无法超过某个值，因此自然考虑如果能让 $a_2$ 达到 $b_2$ 的话，最小的 $x$ 应该是多少。容易发现，$x$ 最小值为 $\lfloor b_2 / 2 \rfloor + 1$。并且，只要 $a_2$ 能使用的 $x$ 这一最小值 $x_m$，那么只要通过两次操作，一次 $x$ 为 $b_2 - x_m$，一次为 $x_m$，即可让 $a_2$ 从 $0$ 变为 $b_2$。

- 由于 $x_m$ 严格比 $b_2$ 的一半多，因此 $b_2 - x_m < x_m$，这意味着只要 $x_m$ 可以使用，那么 $b_2 - x_m$ 也可以使用。

综上，只要 $\lfloor b_2 / 2 \rfloor + 1 \le b_1$，即 $b_2 \le 2 \times b_1 - 1$，那么就可以达成。

从上面两个元素的情况可以得到一个感觉：由于前面的元素会给后面元素的操作集合设置上限，因此一定是越大越好。所以最终操作应该是从左到右依次让 $a_i$ 从 $0$ 变为 $b_i$，而不会跳着操作。

所以最终只需要从左到右依次判断在当前的 $x$ 上限下，$a_i$ 能否从 $0$ 变为 $b_i$，同时在过程中更新 $x$ 的上限即可。

## 代码

```cpp
// Problem: C. Leftmost Below
// Contest: Codeforces - Codeforces Round 1039 (Div. 2)
// URL: https://codeforces.com/problemset/problem/2128/C
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include <algorithm>
#include <cstdio>
#include <cstdlib>

using namespace std;

const int NMAX = 210000;

int T;
int N;
int B[NMAX];

int main() {
    scanf("%d", &T);
    for (int t = 1; t <= T; t += 1) {
        scanf("%d", &N);
        for (int i = 1; i <= N; i += 1) {
            scanf("%d", &B[i]);
        }
        bool can = true;
        int now = B[1];
        for (int i = 2; i <= N; i += 1) {
            if (B[i] >= 2 * now) {
                can = false;
                break;
            }
            now = min(now, B[i]);
        }
        printf("%s\n", can ? "YES" : "NO");
    }
    exit(0);
}
```