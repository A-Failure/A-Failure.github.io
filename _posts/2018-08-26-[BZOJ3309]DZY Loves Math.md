---
layout:     post
title:      "[BZOJ3309]DZY Loves Math"
date:       2018-08-26
author:     "Dispwnl"
header-img: "img/used/456.jpg"
catalog: true
tags:
    - 莫比乌斯反演
---
## [题目](https://www.lydsy.com/JudgeOnline/problem.php?id=3309)
### Description
对于正整数n，定义f(n)为n所含质因子的最大幂指数。例如f(1960)=f(2^3 × 5^1 × 7^2)=3, f(10007)=1, f(1)=0。

给定正整数a,b，求sigma(sigma(f(gcd(i,j)))) (i=1..a, j=1..b)。

### Input
第一行一个数T，表示询问数。

接下来T行，每行两个数a,b，表示一个询问。

### Output
对于每一个询问，输出一行一个非负整数作为回答。

### Sample Input
```
4
7558588 9653114
6514903 4451211
7425644 1189442
6335198 4957
```
### Sample Output
```
35793453939901
14225956593420
4332838845846
15400094813
```
### HINT
#### 【数据规模】

T<=10000

1<=a,b<=10^7

### 题解
题目要求$\sum_{i=1}^{a}\sum_{j=1}^{b}f(\gcd(i,j))$

可以化简成$\sum_{x=1}^{min(a,b)}\sum_{i=1}^{\left\lfloor\frac{a}{x}\right\rfloor}\sum_{j=1}^{\left\lfloor\frac{b}{x}\right\rfloor}[\gcd(i,j)==1]f(x)$

即$\sum_{x=1}^{min(a,b)}f(x)\sum_{i=1}^{\left\lfloor\frac{a}{x}\right\rfloor}\sum_{j=1}^{\left\lfloor\frac{b}{x}\right\rfloor}[\gcd(i,j)==1]$

先不管前面的，有没有发现后面部分很熟悉？

设$F(x)=\sum_{i=1}^{n}\sum_{j=1}^{m}[\gcd(i,j)==x],G(x)=\sum_{x\vert d}F(d)=\left\lfloor\frac{n}{x}\right\rfloor\left\lfloor\frac{m}{x}\right\rfloor$

莫比乌斯反演一下：$F(x)=\sum_{x\vert d}\mu(\frac{d}{x})G(d)$

这样式子就成了：$\sum_{x=1}^{min(a,b)}f(x)\sum_{i=1}^{min(\frac{a}{x},\frac{b}{x})}\mu(i)\left\lfloor\frac{a}{ix}\right\rfloor\left\lfloor\frac{b}{ix}\right\rfloor$

设$T=ix$，则有：$\sum_{T=1}^{min(a,b)}\left\lfloor\frac{a}{T}\right\rfloor\left\lfloor\frac{b}{T}\right\rfloor\sum_{x\vert T}f(x)\mu(\frac{T}{x})$

前面部分就是一个整除分块，后面怎么搞啊QAQ

由于$\mu$函数的性质，设$T$的质因子为$pm_1^{a_1},pm_2^{a_2},...,pm_n^{a_n}$

当$x$里某个$pm_i$的指数$<a_i-1$时，$\mu(\frac{T}{x})=0$

所以$x$有$2^n$种取值（对于每个$pm_i$，ta的指数取$a_i$或$a_i-1$）

假设最高幂次为$k$，即$f(x)=k$

所以$x$有2的指数种取值（比$k$幂次高的都为$a_i$，低的可以是$a_i$也可以是$a_i-1$）

对应着低于$k$次幂的质因子$\frac{T}{x}$里要么有要么没有

所以$\mu(\frac{T}{x})$取值为1或者-1（取决于质因子个数），而$f(x)$的取值一样，所以和为0

这种情况只适用于存在低于$k$次幂的质因子，也就是质因子指数不全相等

另一种情况是质因子指数全相等，即$T=pm_1^apm_2^a...pm_n^a$

还是那样，$\frac{T}{x}$里的质因数指数要么为1要么为0

所以当$x=pm_1^{a-1}pm_2^{a-1}...pm_n^{a-1}$的时候，即每一项的指数都为1

$f(x)=a-1$

而其他情况$x$里面必定有一个$pm_i^a$，所以$f(x)=a$

$\mu(\frac{T}{x})$取值还是为1或者-1

如果是上面的那种情况，一个$a$会对应着一个$-a$，一一抵消和为0

但是这里有且只有一种情况$f(x)=a-1$（设这种情况为$S$），所以和为$0+(a-1-a)\mu(pm_1pm_2...pm_n)$，如果$n$为奇数，$\mu(S)=-1$，原来与它抵消的情况质因子个数为偶数（即$n-1$），所以答案为1，反之答案为-1，所以最后答案为$(-1)^{(n+1)}$

关于这个的处理也很有意思，需要记录每个数去掉最小质因数后剩下的数和最小质因数的次幂，这样小的数先计算，大的数通过小的数计算，线筛中计算就行了

### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
# define LL long long
using namespace std;
const int MAX=1e7+1,MAXN=1e4+1;
int t,a,b,tot,maxn;
int ls[MAX],num[MAX],sum[MAX],pm[MAX];
int qu[MAXN][2];
bool use[MAX];
int main()
{
	scanf("%d",&t);
	for(int i=1;i<=t;++i)
	  scanf("%d%d",&qu[i][0],&qu[i][1]),maxn=max(maxn,max(qu[i][0],qu[i][1]));
	for(int i=2;i<=maxn;++i)
	  {
	  	if(!use[i]) pm[++tot]=i,ls[i]=sum[i]=num[i]=1;
	  	for(int j=1;j<=tot&&i*pm[j]<=maxn;++j)
	  	  {
	  	  	use[i*pm[j]]=1;
	  	  	if(i%pm[j]==0)
	  	  	{
	  	  		num[i*pm[j]]=num[i]+1,ls[i*pm[j]]=ls[i];
	  	  		if(ls[i*pm[j]]==1) sum[i*pm[j]]=1;
	  	  		else if(num[ls[i*pm[j]]]==num[i*pm[j]]) sum[i*pm[j]]=-sum[ls[i*pm[j]]];
	  	  		else sum[i*pm[j]]=0;
	  	  		break;
	  	  	}
	  	  	ls[i*pm[j]]=i,num[i*pm[j]]=1;
			if(num[i]==1) sum[i*pm[j]]=-sum[i];
			else sum[i*pm[j]]=0;
		  }
	  }
	for(int i=1;i<=maxn;++i)
	  sum[i]+=sum[i-1];
	int a=qu[1][0],b=qu[1][1];
	for(int i=1;i<=t;a=qu[++i][0],b=qu[i][1])
	  {
		if(a>b) swap(a,b);
		LL ans=0;
		for(int l=1,r;l<=a;l=r+1)
		  {
		  	int N=a/l,M=b/l;
		  	r=min(a/N,b/M),ans+=1ll*N*M*(sum[r]-sum[l-1]);
		  }
		printf("%lld\n",ans);
	  }
	return 0;
}
```
