---
tags:
  - Codeforces
  - 贪心
---
题目链接：[链接](https://codeforces.com/problemset/problem/2149/A)

## 题意

给定一个包含 $n$ 个元素的数组 $a$，其中每个元素等于 $-1$、$0$ 或 $1$。在一次操作中，你可以选择一个索引 $i$ 并将 $a_i$ 增加 $1$（即，赋值 $a_i \gets a_i + 1$）。操作可以执行任意多次，选择任意索引。

目标是通过最少的操作次数使数组中所有元素的乘积严格为正，即 $a_1 \cdot a_2 \cdot a_3 \cdot \ldots \cdot a_n > 0$ 。求最小操作次数。

保证这总是可能的。

## 题解

如果要乘积为正，那么需要满足以下几个条件：

1. 没有 $0$；
2. 负数的数量为偶数。

由于每次只能加一，因此

- 对于 $0$，需要通过 $1$ 次操作让数变成 $1$。由于结果一定是正数，因此不影响上面第二个条件是否成立。
- 对于奇数个 $-1$，需要使用两次操作让其中一个变成 $2$。因此如果有奇数个一，需要额外两次操作。

综上，只需要统计 $0$ 的个数，以及 $-1$ 的个数，然后判断输出即可。如果负数的个数为奇数，答案为 $0$ 的个数加二；否则，答案为 $0$ 的个数。

## 代码

```cpp
// Problem: A. Be Positive
// Contest: Codeforces - Codeforces Round 1054 (Div. 3)
// URL: https://codeforces.com/problemset/problem/2149/A
// Memory Limit: 256 MB
// Time Limit: 1000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include <cstdio>
#include <cstdlib>

using namespace std;

const int NMAX = 10;
const int CMAX = 3;
const int C_BASE = 1;

int T;
int N;
int A[NMAX];

int main() {
	scanf("%d", &T);
	for(int i = 1;i <= T;i += 1) {
		scanf("%d", &N);
		int cnt[CMAX] = {};
		for(int i = 1;i <= N;i += 1) {
			int x;
			scanf("%d", &x);
			cnt[x + C_BASE] += 1;
		}
		int ans = cnt[0 + C_BASE];
		if((cnt[-1 + C_BASE] & 1) != 0) {
			ans += 2;
		}
		printf("%d\n", ans);
	}
	exit(0);
}
```