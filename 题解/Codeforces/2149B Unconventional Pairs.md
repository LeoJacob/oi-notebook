---
tags:
  - Codeforces
  - 贪心
---
题目链接：[链接](https://codeforces.com/problemset/problem/2149/B)

## 题意

给定一个包含 $n$ 个整数的数组 $a_1, a_2, \dots, a_n$。已知 $n$ 是偶数。要求将数字两两配对，分成恰好 $n/2$ 对 $(a_{p_1}, a_{q_1}), (a_{p_2}, a_{q_2}), \dots, (a_{p_{n/2}}, a_{q_{n/2}})$。每个下标最多只能出现在一个对中。

对于一个对 $(x, y)$，其差值定义为 $|x - y|$。求在所有配对方案中，所有对中的最大差值的最小值是多少。

## 题解

因为代价为 $|x - y|$，因此配对的两者离的越近越好。所以一个朴素的想法是排序之后从前到后两个一组分别配对。

接下来证明一下这个配对方法是最优的。仍然从极端元素考虑，这里选取最小的（选择最大的类似）。

假设 $a_1, a_2, \dots, a_n$ 已经按从小到大的顺序排好序。那么根据猜测的策略，$a_1, a_2$ 应该构成一对。假如和 $a_1$ 配对的不是 $a_2$，比如说是 $a_i$，我们尝试通过调整法来说明猜测的策略是最优的。

现在 $(a_1, a_i)$ 构成一对，想要调整成 $(a_1, a_2)$，那么自然同样要关注当下和 $a_2$ 配对的元素 $a_j$。

因此，现在 $(a_1, a_i)$ 构成一对，$(a_2, a_j)$ 构成一对。接下来尝试同配对 $(a_1, a_2), (a_i, a_j)$ 相比，谁更优秀。

对于 $(a_1, a_i), (a_2, a_j)$，代价为 $\max(a_i - a_1, a_j - a_2)$；对于 $(a_1, a_2),(a_i, a_j)$，代价为 $\max(a_2 - a_1, |a_i - a_j|)$。可以尝试讨论 $a_i, a_j$ 的关系画下图观察一下大小关系。如下图所示：![[2149B Unconventional Pairs.excalidraw#^clippedframe=ZaYg5nhi0UtpqY-cHDl7J|100%]]

容易发现，无论 $a_i, a_j$ 之间大小关系如何，$(a_1, a_2), (a_i, a_j)$ 配对的方式都不可能比 $(a_1, a_i), (a_2, a_j)$ 配对的方式更差。因此必定存在一个最优解，使得 $(a_1, a_2)$ 配为一对。

> [!important] 并非所有最优解都有 $(a_1, a_2)$ 配为一对
> 
> 注意，上面的推理过程**只能**说明至少有一种最优解满足 $(a_1, a_2)$ 配为一对，但是不能说明**所有**最优解都是这样的。
> 
> 例如，对于 $\{a_i\} = \{1, 2, 3, 4, 5, 8\}$，容易验证，$\{(1, 2), (3, 4), (5, 8)\}$ 和 $\{(1, 3), (2, 4), (5, 8)\}$ 都是最优解，但是后者并不满足最小的两者互相配对。

既然已经确定一定有最优解满足 $(a_1, a_2)$ 配为一对，那么强制让两者配对肯定不会漏掉最优解。如此做以后，问题已经从如何配对 $a_1, a_2, \dots, a_n$，变为只需要考虑如何配对 $a_3, a_4, \dots, a_n$，规模从一个 $n$ 的问题变为了 $n - 2$ 的问题。重复应用上面的观点，即可说明猜测的策略一定是最优解之一。

## 代码

```cpp
// Problem: B. Unconventional Pairs
// Contest: Codeforces - Codeforces Round 1054 (Div. 3)
// URL: https://codeforces.com/contest/2149/problem/B
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
int N;
int A[NMAX];

int main() {
	scanf("%d", &T);
	for(int t = 1;t <= T;t += 1) {
		scanf("%d", &N);
		for(int i = 1;i <= N;i += 1) {
			scanf("%d", &A[i]);
		}
		sort(A + 1, A + N + 1);
		int ans = 0;
		for(int i = 1;i <= N;i += 2) {
			ans = max(ans, A[i + 1] - A[i]);
		}
		printf("%d\n", ans);
	}
	exit(0);
}
```