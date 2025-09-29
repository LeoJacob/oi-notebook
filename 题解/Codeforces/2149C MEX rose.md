题目链接：[链接](https://codeforces.com/problemset/problem/2149/C)

## 题意

给定一个长度为 $n$ 的数组 $a$ 和一个整数 $k$，其中 $0 \leq k \leq n$。在一次操作中，你可以选择任意下标 $i$（$1 \leq i \leq n$）并将 $a_i$ 设置为区间 $[0, n]$ 中的任意整数 $x$。

求满足条件 $\text{MEX}(a) = k$ 所需的最小操作次数。

* $\text{MEX}(a)$ 是数组 $a_1, a_2, \dots, a_n$ 中未出现的最小非负整数。

## 题解

为了令 $\text{MEX}(a) = k$，需要满足以下两个条件：

1. $a$ 中没有值为 $k$ 的元素；
2. 对于 $v \in \{0, 1, \dots, k - 1\}$，$a$ 中至少有一个元素的值为 $v$。

显然，对于 $a$ 中值为 $k$ 的元素，是必须要修改为其他值的，设这种元素有 $x$ 个，则必须要付出 $x$ 的操作次数。

在修改时，可以尝试同时满足第二个条件。设在 $\{0, 1, \dots, k - 1\}$ 中有 $y$ 种值之前不存在，那么最多可以填补 $x$ 个缺少的种类，剩余的则需要修改其他元素来填补。易得该部分需要的额外操作数为 $\max(0, y - x)$。

因此，最终答案为 $x + \max(0, y - x)$。

## 代码

```cpp
// Problem: C. MEX rose
// Contest: Codeforces - Codeforces Round 1054 (Div. 3)
// URL: https://codeforces.com/problemset/problem/2149/C
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include <cstdio>
#include <cstdlib>
#include <algorithm>

using namespace std;

const int NMAX = 210000;

int T;
int N, K;
int A[NMAX];
int cnt[NMAX];

int main() {
	scanf("%d", &T);
	for(int t = 1;t <= T;t += 1) {
		scanf("%d %d", &N, &K);
		for(int i = 0;i <= K;i += 1) {
			cnt[i] = 0;
		}
		for(int i = 1;i <= N;i += 1) {
			scanf("%d", &A[i]);
			cnt[A[i]] += 1;
		}
		int n_need = 0;
		for(int i = 0;i < K;i += 1) {
			if(!cnt[i]) {
				n_need += 1;
			}
		}
		int ans = cnt[K] + max(0, n_need - cnt[K]);
		printf("%d\n", ans);
	}
	exit(0);
}
```