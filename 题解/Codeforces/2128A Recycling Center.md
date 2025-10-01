---
tags:
  - Codeforces
  - 贪心
---

题目链接：[链接](https://codeforces.com/problemset/problem/2128/A)

## 题意

有 $n$ 个垃圾袋，第 $i$ 个袋子的重量为 $a_i$。每秒以下两个动作依次发生：

1. 首先，必须选择一个垃圾袋并销毁它。如果该袋子的重量严格大于 $c$，则花费 $1$ 硬币；否则花费 $0$ 硬币。
2. 然后，每个剩余垃圾袋的重量乘以 $2$。

问销毁所有垃圾袋所需的最小硬币数是多少？

## 题解

显然，每个垃圾袋是否需要付费只跟销毁它的时间有关。容易通过计算得到每个垃圾袋需要付费的分界线 $t_i$：当且仅当销毁 $i$ 的时间 $> t_i$ 时，需要花费 $1$ 金币。

现在问题转化为，给定分界线 $\{t_i\}$，要求安排销毁顺序，使得销毁时间 $\le t$ 的垃圾袋的数量尽可能多。

根据考虑“极端元素”的原则，可以有以下四种考虑方向：

| 对象  | 能力最强       | 能力最差       |
| --- | ---------- | ---------- |
| 时间  | 从 $1$ 开始   | 从 $n$ 开始   |
| 垃圾袋 | 从 $t$ 最大开始 | 从 $t$ 最小开始 |

从能力最强考虑通常是基于以下经验：在最优化一件事情的时候，如果能力最强的没有产生贡献，那么肯定是吃亏的。

从能力最弱考虑则通常是基于以下经验：能力较强的元素有更高的灵活性，可以作为“机动元素”存在，按需进行安排；而能力较差的元素由于本身的限制，通常只能在非常有限的位置安排，不能任意进行调整。

- 例如，沟通同学聚会时间时，通常会优先考虑可用时间最少的那些同学。

无论从哪种角度考虑，一般来说，需要找到一些必要条件，推出“完成某种操作 $X$ 一定不会更差”，继而推导出一定存在最优解包含 $X$，然后只关注包含 $X$ 的所有解，尝试在这一条件下转化为某种更小规模的原问题，通过递归应用贪心观点，最终解决原问题。

下面给出两个例子，说明具体的考虑方法。由于推理方法上的类似性，其余两个方向在此省略。仅在代码中给出了所有四种方向的实现。

### 示例角度 1：能力最差 + 时间

尝试考虑“能力最差”的时间，显然是最大的时刻。对于这一时刻 $T$，考虑 $t_i \ge T$ 的所有任务，如果不存在，那么显然这一时刻销毁任一垃圾袋都会导致 $1$ 花费；否则，意味着有垃圾袋 $x$ 可以在这一时刻免费被销毁。

如果不选择在这个时刻免费销毁某一垃圾袋，而选择了付费销毁某一个 $y$，那么设 $x$ 在 $T' < T$ 被销毁，交换两者，即在 $T$ 时刻销毁 $x$ 而在 $T'$ 时刻销毁 $y$。显然 $x$ 是免费销毁的，而 $y$ 由于在更早时间销毁，可能从付费变为免费，因此交换后不会更差，所以必定有最优解会在该时刻免费销毁某一垃圾袋。

容易发现，由于 $T$ 是最大的时刻，在这一时刻能免费销毁，意味着在其他时刻都可以免费销毁，因此选谁其实是无所谓的。

任选一个销毁以后，由于最后一个时刻的决策已经确定，因此可以认为不存在。问题从“决策在 $n$ 个时刻销毁哪个垃圾袋”，变为了规模更小的“决策在 $n - 1$ 个时刻销毁哪个垃圾袋”的问题。重复应用上面的观点，即可解决该问题。

总结来说，具体流程为：统计当前最大时刻能免费销毁的垃圾袋数目，如果没有，答案加一；否则，该数目减一，然后把剩下的数目传递给前一时刻，同时和在前一时刻是免费销毁最后日期的垃圾袋一起，作为前一时刻能免费销毁的垃圾袋数目，然后重复该过程。

细节可以参考代码。

### 示例角度 2：能力最强 + 垃圾袋

尝试考虑“能力最强”的垃圾袋 $x$，显然是 $t$ 最大的。如果 $x$ 不可以被免费销毁，那么所有垃圾袋均不可以被免费销毁。下面假设 $x$ 可以被免费销毁。

既然 $x$ 是能力最强的，那么如果有其它垃圾袋 $y$ 可以免费销毁，而 $x$ 不免费，那么将 $x, y$ 的销毁时刻对调，可以发现答案不变。因此可以推出，一定有最优解满足 $x$ 可以被免费销毁。下面只考虑 $x$ 被免费销毁的解。

已经确定 $x$ 必定免费销毁，仍需要决策在何时免费销毁。由于时间越小越能使 $t$ 更小的垃圾袋被免费销毁，因此为了给剩下垃圾袋留下更多的选择，应该选择能使用的最靠后的时间销毁 $x$。

确定了 $x$ 必定在某个时刻免费销毁以后，问题规模从 $n$ 变为 $n - 1$。重复应用上面的观点，即可解决该问题。

## 代码

以下代码给出了四种考虑方向的实现，命名为 `sol_<best|worst>_<time|bag>()`，代表方向可参考注释。可在行 105 至 108 部分通过取消特定方法注释决定具体启用哪一种方向。

```cpp
// Problem: A. Recycling Center
// Contest: Codeforces - Codeforces Round 1039 (Div. 2)
// URL: https://codeforces.com/problemset/problem/2128/A
// Memory Limit: 256 MB
// Time Limit: 1000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include <cstdio>
#include <cstdlib>
#include <algorithm>
#include <functional>

using namespace std;

const int NMAX = 35;

int T;
int N, C;
int A[NMAX];
int ddl[NMAX];

int sol_worst_time() { // 能力最差 + 时间
	sort(ddl + 1, ddl + N + 1);
	int pos = N;
	int n_can = 0;
	int ans = 0;
	for(int i = N;i >= 1;i -= 1) {
		while(pos > 0 && ddl[pos] >= i) {
			n_can += 1;
			pos -= 1;
		}
		if(!n_can) {
			ans += 1;
		} else {
			n_can -= 1;
		}
	}
	return ans;
}

int sol_best_time() { // 能力最好 + 时间
	sort(ddl + 1, ddl + N + 1);
	int pos = 1;
	int ans = 0;
	for(int i = 1;i <= N;i += 1) {
		while(pos <= N && ddl[pos] < i) {
			ans += 1;
			pos += 1;
		}
		if(pos <= N) {
			pos += 1;
		}
	}
	return ans;
}

int sol_worst_bag() { // 能力最差 + 垃圾袋
	sort(ddl + 1, ddl + N + 1);
	int n_can = 0;
	int ans = 0;
	for(int i = 1;i <= N;i += 1) {
		n_can += ddl[i] - ddl[i - 1];
		if(!n_can) {
			ans += 1;
		} else {
			n_can -= 1;
		}
	}
	return ans;
}

int sol_best_bag() { // 能力最好 + 垃圾袋
	sort(ddl + 1, ddl + N + 1, greater<int>());
	int last_can = N;
	int ans = 0;
	for(int i = 1;i <= N;i += 1) {
		last_can = min(last_can, ddl[i]);
		if(last_can > 0) {
			last_can -= 1;
		} else {
			ans = N - i + 1;
			break;
		}
	}
	return ans;
}

int main() {
	scanf("%d", &T);
	for(int t = 1;t <= T;t += 1) {
		scanf("%d %d", &N, &C);
		for(int i = 1;i <= N;i += 1) {
			scanf("%d", &A[i]);
		}
		for(int i = 1;i <= N;i += 1) {
			ddl[i] = 0;
			int tmp = A[i];
			while(tmp <= C) {
				ddl[i] += 1;
				tmp <<= 1;
			}
		}
		int ans;
		// ans = sol_worst_time();
		// ans =  sol_best_time();
		ans = sol_worst_bag();
		// ans = sol_best_bag();
		printf("%d\n", ans);
	}
	exit(0);
}
```