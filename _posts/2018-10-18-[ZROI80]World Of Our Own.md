---
layout:         post
title:          "[ZROI80]World Of Our Own"
date:           2018-10-18
author:         "Dispwnl"
header-img:     "img/used/112.jpg"
catalog:    true
tags:
    - 瞎搞
    - 组合数学
---
## [题目](http://www.zhengruioi.com/problem/80)
### 题目描述
小C在研究数组的xor。

她有一个长度为$n$的数组$A$。

她每次会把$a_{i-1}$和$a_i$异或起来，然后$A$的长度会变成$n - 1$

这么操作了$n - 1$次之后得到了长度为$1$的数组。

她想知道在操作过程中每次的$a_1$为多少?

为了避免输出过大，请输出第$j$次操作后的$a_1 \times (j + 1)$的异或和($0 \leq j \leq n - 1$)。

### 输入格式
一行五个整数$n$, $a$, $b$, $c$, $d$。

数组$A$以如下方式生成，$a_1 = a$, $a_i = (a_{i-1}^2 + b \times a_{i-1} + c) \mod d$

### 输出格式
一行一个整数表示答案。

### 样例一
#### input
```plain
5 3 2 4 15
```
#### output
```plain
89
```
#### 样例解释
```plain
3 4 13 4 13 
7 9 9 9
14 0 0
14 0
14
```
$3 \ xor \ (7 * 2) \ xor \ (14 * 3) \ xor \ (14 * 4) \ xor \ (14 * 5) = 89$

### 限制与约定
$30\%$的分数，$n \leq 10^3, a, b, c, d \leq 10^9$

另外$10\%$的分数， $d = 2$

另外$30\%$的分数，$d \leq 50$

$100\%$的分数，$n \leq 8 \times 10^6, a, b, c, d \leq 10^9, a < d$

1s, 512MB

### 题解
~~感觉像我这种辣鸡一辈子都想不出来怎么做~~

[感觉这里讲的挺清楚的](https://blog.csdn.net/FYOIER/article/details/80955367)

考虑每一个数字是怎么参与贡献的

从最初始的数组看，要是ta是第$i-1$位，相当于向上走了一步；要是ta是第$i$位，相当于向左上走了一步

那么位置$i$走了$j$步到达位置$0$（即$a_1$）的方案数为$C_{j}^{i}$，而当这个方案数是偶数的时候异或值为$0$，所以只有奇数计入贡献

所以就是考虑$C_{j}^{i}\% 2=1$，$2$是质数所以可以想到（？）$lucas$定理，$C_{j}^{i}\% 2=C_{\left\lfloor\frac{j}{2}\right\rfloor}^{\left\lfloor\frac{i}{2}\right\rfloor}\times C_{j\% 2}^{i\% 2}\% 2$，$i\%2,j\%2$分别有两种取值（$0$或$1$），只有当$i\%2=1,j\%2=0$的时候$C_{j\% 2}^{i\% 2}$答案为$0$，即$i$为奇数，$j$为偶数，考虑到$lucas$是相当于将$i,j$二进制最后一位提出来然后删掉，必须保证某一位$j$为$0$的时候$i$不能为$1$，即$i$为$j$的二进制子集，$a_i$能被统计进第$j$步的答案

所以要统计$j$的子集的异或和，因为自己子集的子集还是自己的子集，所以可以用前缀和优化

~~思维难度上天代码只有20行~~
### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<cmath>
# include<algorithm>
# define LL long long
using namespace std;
const int MAX=8e6+5;
LL n,b,c,d,ans,qwq;
LL a[MAX],sum[MAX];
int main()
{
	scanf("%lld%lld%lld%lld%lld",&n,&a[0],&b,&c,&d),qwq=log2(n),sum[0]=a[0];
	for(int i=1;i<n;++i)
	  a[i]=(a[i-1]*a[i-1]%d+b*a[i-1]%d+c%d)%d,sum[i]=a[i];
	for(int i=0;i<=qwq;++i)
	  for(int j=0;j<n;++j)
	    if((j>>i)&1) sum[j]^=sum[j^(1<<i)];
	for(int i=0;i<n;++i)
	  ans^=sum[i]*(i+1); 
	return printf("%lld",ans),0;
}
```
