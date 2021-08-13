---
title: 题解：CF1407D Discrete Centrifugal Jumps
date: 2021-08-12 15:13:32
tags: OI/题解
cover: true
seo_title: 关于
top_meta: true
bottom_meta: true
music:
  server: netease   # netease, tencent, kugou, xiami, baidu
  type: song        # song, playlist, album, search, artist
  id: 16846091      # song id / playlist id / album id / search keyword
mathjax: true
group: group-1
order: 16
sidebar: [group-1, toc]
valine:
  placeholder: 你觉得这篇文章怎么样呢？
---

## 题意简析

{% note 给你 $n$ 座楼，没座楼的高度为 $h_i$。%}
{% note 初始在第一座楼，你需要跳到第 $n$ 座楼上。%}
{% note 能从 $i$ 跳到 $j$ 需满足以下条件之一：%}

$1. i = j + 1$

$2. \min(h_i,h_j) > \max(h_{i+1},h_{i+2},...,h_{j-2},h_{j-1})$

$3. \max(h_i,h_j) < \min(h_{i+1},h_{i+2},...,h_{j-2},h_{j-1})$

{% note 问你最小需要跳几次？%}
{% note  $2\le n \le 3\times 10^5$,$1\le h_i \le 10^9$。%}

## 分析

显然，这是一道 dp 题。但是直接暴力枚举 $i,j$，和枚举区间最大值和最小值，复杂度为 $O(n^3)$。可能某些神仙能把它优化到 $O(n^2)$，但是本题仍不能过。考虑 $O(n\log n)$ 的算法。

这里考虑单调栈。什么是单调栈？

单调栈，是指一个栈中，所有的元素均满足一种**单调性**。它可以是升序，也可以是降序，只要满足单调皆可。

在这里，区间的最大值，最小值，都可以存到一个单调栈中，每次需要查找或更新时，将栈中的元素取出即可。这一复杂度为 $O(\log n)$。~~就很神仙。~~

那么在本题中，我们可以开两个栈，分别为升序和降序，表示题意中最大值和最小值能跳跃的条件。方便规定，我们将栈顶元素最大表示为降序。记 $s$ 表示降序的单调栈，且 $s$ 中保存的元素是它在 $h$ 中的编号。

对于这种情况：假设我们当前枚举到了 $i$，让 $h_i$ 与栈顶 $h_{s_{tot}}$ 进行比较。如果 $h_i\le h_{s_{tot}}$，违背了单调性，需要让 $tot=tot-1$，但是在这种情况，满足我们跳跃的第二种条件。因为单调性，所以 $h_{s_{tot-1}}\le h_{s_{tot}}$，且 $h_{s_{tot}}$ 是 $[tot,i]$ 中的最大值，又已知  $h_i\le h_{s_{tot}}$，只要$h_i\not = h_{s_{tot}}$ 肯定满足 $\min(h_i,h_{s_{tot-1}})<\max(h_{s_{tot}},...,h_{i-1})$，固有：
$$dp_i=\min(dp_i,dp_{s_{tot-1}}+1)$$

升序同理。
当然，最初
$$dp_i=dp_{i-1}+1$$

这样我们就轻松的解决了这道题。
## 总结
~~其实也没啥好总结的。~~ 用单调栈优化 dp。

```cpp
#include <bits/stdc++.h>

using namespace std;
const int N = 3e5 + 7;;
int s[N], dp[N], a[N], b[N];
int tot1, tot2;

inline int max(int x, int y){return x > y ? x : y;}
inline int min(int x, int y){return x < y ? x : y;}

inline int read(){
	int x = 0, f = 1; char c = getchar();
	while (!isdigit(c)){if (c == '-') f = -1; c = getchar();}
	while (isdigit(c)){x = (x << 3) + (x << 1) + c - '0'; c = getchar();}
	return x * f;
}

int main(){
	int n = read();
	for (int i = 1; i <= n; i ++) s[i] = read();
	memset(dp, 0x3f3f3f3f, sizeof(dp));
	a[++ tot1] = 1, b[++ tot2] = 1, dp[1] = 0;
	for (int i = 2; i <= n; i ++){
		dp[i] = dp[i - 1] + 1;
		while (tot1 && s[i] >= s[a[tot1]]){
			if (s[i] != s[a[tot1]]) dp[i] = min(dp[i], dp[a[tot1 - 1]] + 1);
			tot1 --;
		}
		while (tot2 && s[i] <= s[b[tot2]]){
			if (s[i] != s[b[tot2]]) dp[i] = min(dp[i], dp[b[tot2 - 1]] + 1);
			tot2 --;
		} 
		a[++ tot1] = i, b[++ tot2] = i;
	}
	printf("%d", dp[n]);
	return 0;
}
```