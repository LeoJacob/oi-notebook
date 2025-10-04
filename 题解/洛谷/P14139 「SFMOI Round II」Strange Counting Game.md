---
tags:
  - 洛谷
  - 数论
---
题目链接：[链接](https://www.luogu.com.cn/problem/P14139)

## 题意

给定 $n \le 10^{18}$，求

$$
\sum_{x=1}^{n} \left\lfloor \frac{x^2}{n} \right\rfloor + \left\lfloor \sqrt{nx} \right\rfloor
$$

答案对 $10^9+7$ 取模。

## 题解

下取整不好处理。联想到 [数论分块](https://oi.wiki/math/number-theory/sqrt-decomposition/) 的相关技巧，尝试对每个值计算在和式中出现的次数。

如果 $i = \lfloor x^2 / n \rfloor$，则意味着 $i \le x^2 / n < i + 1$，解得 $\left\lceil \sqrt{in} \right\rceil \le x \le \left\lceil \sqrt{(i + 1)n} \right\rceil - 1$。

因此，

$$
\begin{aligned}
\sum_{x=1}^{n} \left\lfloor \frac{x^2}{n} \right\rfloor + \left\lfloor \sqrt{nx} \right\rfloor &= \sum_{i=1}^{n - 1} i\left(\left\lceil \sqrt{(i + 1)n} \right\rceil - \left\lceil \sqrt{in} \right\rceil\right) + n + \sum_{x=1}^n \left\lfloor \sqrt{nx} \right\rfloor\\
&= \sum_{i=1}^{n-1}\left(-\left\lceil \sqrt{in} \right\rceil\right) + (n - 1) \times n + n + \sum_{x=1}^n \left\lfloor \sqrt{nx} \right\rfloor\\
&=n^2 + \sum_{i=1}^{n-1}\left(-\left\lceil \sqrt{in} \right\rceil + \left\lfloor\sqrt{in}\right\rfloor\right) + n\\
&=n^2 + n + \sum_{i=1}^{n-1}-[\text{$in$ 不是完全平方数}]\\
&=n^2 + n - \left(n - \sum_{i=1}^{n}[\text{$in$ 是完全平方数}]\right)\\
&=n^2 + \sum_{i=1}^{n}[\text{$in$ 是完全平方数}].
\end{aligned}
$$

现在只需要分析 $in$ 中有多少个完全平方数即可。设 $n = a \times b^2$，其中 $b$ 是 $n$ 最大的平方因子，则为了 $in$ 是完全平方数，$i$ 需要为 $a \times x^2$ 的形式。易得 $x$ 恰好可以取遍 $1, 2, \dots, b$。因此，只要把 $n$ 分解成 $a \times b^2$ 的形式，那么答案即为 $n^2 + b$。

如果 $n$ 比较小，那么显然直接质因数分解即可。然而由于 $n$ 太大，并不能直接得到 $n$ 的所有质因子。联想到在分解 $10^9$ 的整数时，只需要处理 $\sqrt{10^9}$ 的质数，剩余如果有则一定是一个质数，因此可以设能把 $B$ 以内的质因子都分解出来，看看剩下的部分能不能借由不等式导出一些简单的关系。

假设分解完毕，那么 $a, b$ 如果没分解完，必定剩余的部分是大于 $B$ 的。假设最坏情况下，两者都没分解完，那么剩余的部分必定大于 $B^3$。可以发现，如果令 $B = 10^6$，那么由于 $n \le 10^{18} = B^3$，两者都没分解完是不可能的。因此，剩余的部分要么是 $a$，要么是 $b^2$。只需要判断剩余部分是不是一个完全平方数即可确定是哪种情况。

复杂度为 $O(T \times n^{1/3} / \ln n)$。

## 代码

```c++
// Problem: P14139 「SFMOI Round II」Strange Counting Game
// Contest: Luogu
// URL: https://www.luogu.com.cn/problem/P14139?contestId=277395
// Memory Limit: 512 MB
// Time Limit: 2500 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include <cstdio>
#include <cstdlib>
#include <cmath>
#include <vector>

using namespace std;

const int PMAX = 1100000;
const int MOD = 1000000007;


int T;
long long N;
vector<int> prime;
bool not_prime[PMAX];

void gen_prime() {
	not_prime[1] = true;
	for(int i = 2;i < PMAX;i += 1) {
		if(!not_prime[i]) {
			prime.push_back(i);
		}
		for(int j = 0;j < (int)prime.size() && i * prime[j] < PMAX;j += 1) {
			not_prime[i * prime[j]] = true;
			if(i % prime[j] == 0) {
				break;
			}
		}
	}
}

int solve(long long n) {
	int ans = (n % MOD) * (n % MOD) % MOD;
	long long a = 1, b = 1;
	for(auto p: prime) {
		int cnt = 0;
		while(n % p == 0) {
			n /= p;
			cnt += 1;
			if(cnt % 2 == 0) {
				b *= p;
			}
		}
		if(cnt % 2 == 1) {
			a *= p;
		}
	}
	long long rt = sqrtl(n);
	if(rt * rt == n) {
		b *= rt;
	} else {
		a *= n;
	}
	return (ans + b) % MOD;
}

int main() {
	gen_prime();
	scanf("%d", &T);
	for(int t = 1;t <= T;t += 1) {
		scanf("%lld", &N);
		printf("%d\n", solve(N));
	}
	exit(0);
}
```