---
tags:
  - Codeforces
  - 构造
---

题目链接：[链接](https://codeforces.com/problemset/problem/2128/B)

## 题意

我们称一个大小为 $n$ 的数组 $a$ 是坏的，当且仅当存在一个下标 $i$（$1 \leq i \leq n-4$）使得以下条件之一成立：

- $a_i < a_{i+1} < a_{i+2} < a_{i+3} < a_{i+4}$，
- $a_i > a_{i+1} > a_{i+2} > a_{i+3} > a_{i+4}$。

否则，数组是好的。

给定一个排列 $p_1, p_2, \dots, p_n$，需要进行 $n$ 轮操作，每次可以移除剩余数组的最左端或最右端元素。给出一种移除元素的方法，使得移除的元素按顺序构成的序列是好的。

可以证明在给定约束下总是存在这样的方案。

## 题解

分析一下条件，实质上在说不可以有连续 $5$ 个元素构成单调序列。直觉上，这意味着序列需要不断在上升/下降反复震荡，以避免出现过长的单调序列。

首先尝试一下在较短的序列上如何实现震荡。容易发现，对于 $3$ 个元素的序列，总可以通过控制最大值在中间构造出山峰。同理，可以通过控制最小值在中间，从而构造出山谷。

如果可以交替得到山峰和山谷，那么最长的单调序列最多是 $4$，本题就做完了。

下面尝试在较长的序列上利用两端的元素看看能否仍能得到山峰和山谷。可以发现总是可以做到的。以控制最小值在中间为例，假设 `m` 是最小值，`x` 和 `y` 是其他元素，有：

- 对于 `mx....y`，可以通过 `RLL`；
- 对于 `xm....y`，可以通过 `LLR`；
- 对于 `xy....m`，可以通过 `LRL`。

每三个一组交替构造山峰和山谷即可。最后如果剩一个/两个，可以随意接到后面，也不会违反条件。

## 代码

```cpp
// Problem: B. Deque Process
// Contest: Codeforces - Codeforces Round 1039 (Div. 2)
// URL: https://codeforces.com/problemset/problem/2128/B
// Memory Limit: 256 MB
// Time Limit: 1500 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include <cstdio>
#include <cstdlib>

using namespace std;

const int NMAX = 210000;

int T;
int N;
int P[NMAX];

void nxt(int &l, int &r, int mode) {
	int cand[3] = {l, l + 1, r};
	int opt_idx = l;
	for(int i = 0;i < 3;i += 1) {
		bool win = mode?P[opt_idx] > P[cand[i]]:P[opt_idx] < P[cand[i]];
		if(win) {
			opt_idx = cand[i];
		}
	}
	if(opt_idx == l) {
		printf("RLL");
	} else if (opt_idx == l + 1) {
		printf("LLR");
	} else {
		printf("LRL");
	}
	l += 2;
	r -= 1;
}

int main() {
	scanf("%d", &T);
	for(int t = 1;t <= T;t += 1) {
		scanf("%d", &N);
		for(int i = 1;i <= N;i += 1) {
			scanf("%d", &P[i]);
		}
		int l = 1, r = N;
		int mode = 0;
		while(r - l + 1 >= 3) {
			nxt(l, r, mode);
			mode = 1 - mode;
		}
		while(l <= r) {
			printf("L");
			l += 1;
		}
		printf("\n");
	}
	exit(0);
}
```