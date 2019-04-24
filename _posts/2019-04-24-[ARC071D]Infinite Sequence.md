---
layout:		post
title:		"[ARC071D]Infinite Sequence"
date:		2019-04-24
author:		"Dispwnl"
header-img:	"img/used/230440.jpg"
catalog:	false
tags:
    - 动态规划
---

## [题目](<https://arc071.contest.atcoder.jp/tasks/arc071_d>)

### 题目大意

求有多少个无限长的由${1,2...,n}$组成的序列$a1,a2...$满足以下条件:

1.第$n$个及以后的元素是相同的，即若$n\le i,j,ai=aj$。

2.对于每个位置$i$，紧随第i*i*个元素后的$ai$个元素是相同的，即若$i<j<k≤i+ai,aj=ak$

输入$n$,请输出序列数量 $\bmod 1000000007$

### Sample Input 1
```plain
2
```

### Sample Output 1
```plain
4
```
### Sample Input 2

```plain
654321
```

### Sample Output 2

```plain
968545283
```

### 题解

发现一个性质：

> 如果有两个不为$1$的数放在了一起，那么后面的数只有一种情况

所以设$f_i$表示第$i$位到$n$形成的序列数量，初始时有$f_n=n,f_{n-1}=n^2$（随便填）

考虑转移

- 如果当前填的数$x\not =1$，后面紧跟着填$x$个$1$，那么方案数为$\sum_{j=i+3}^nf_j$，
- 如果当前填的数$x\not =1$，后面紧跟着的数$y\not =1$，那么方案数为$(n-1)^2$（大于$1$的随便填，后面的只有一种填法）
- 如果当前填的数$x=1$，那么方案数为$f_{i+1}$

发现好像少了点什么？第一种情况时如果$i+x\ge n$时也是只有一种填法，所以有$n-i\le x\le n$，所以要加上$n-(n-i)+1=i+1$

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
const int MAX=1e6+5,mod=1e9+7;
int n;
int f[MAX];
int main()
{
	scanf("%d",&n),f[n]=n,f[n-1]=1ll*n*n%mod;
	for(int i=n-2,sum=0;i>=1;--i)
	  (sum+=f[i+3])%=mod,(f[i]+=((1ll*(n-1)*(n-1)%mod+f[i+1])%mod+i+1+sum)%mod)%=mod;
	return printf("%d",f[1]),0;
}
```

