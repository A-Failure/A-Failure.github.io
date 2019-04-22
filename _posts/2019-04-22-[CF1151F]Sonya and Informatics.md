---
layout:		post
title:		"[CF1151F]Sonya and Informatics"
date:		2019-04-22
author:		"Dispwnl"
header-img:	"img/used"
catalog:	false
tags:
    - 动态规划
    - 矩阵乘法
---

## [题目](<https://www.luogu.org/problemnew/show/CF1151F>)

### 题解

首先先考虑$k$较小的时候怎么做，设$f_{i,j}$表示到第$i$次操作，设$N_{0/1}$为$0/1$的个数，前$N_0$个数中有$j$个$1$的概率

设$M=\frac{n(n-1)}{2}$，则有转移$f_{i,j}=\left(\frac{\frac{N_1(N_1-1)}{2}+\frac{N_0(N_0-1)}{2}+j(N_1-j)+j(N_0-j)}{M}\right)f_{i,j}+\frac{(j+1)^2}{M}f_{i,j+1}+\frac{(N_1-j+1)(N_0-j+1)}{M}f_{i,j-1}$

这个可以用矩阵快速幂优化，复杂度$O(n^3\log k)$

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
const int N=105,mod=1e9+7;
struct Mx{
	int a[N][N];
}a,b,emp;
int n,k,n0,n1,inv,n_;
int f[N][N];
bool A[N];
Mx operator* (Mx a,Mx b)
{
	Mx c=emp;
	for(int i=0;i<=n_;++i)
	  for(int j=0;j<=n_;++j)
	    for(int k=0;k<=n_;++k)
	      {
	      	c.a[i][j]+=1ll*a.a[i][k]*b.a[k][j]%mod;
	      	if(c.a[i][j]>=mod) c.a[i][j]-=mod;
		  }
	return c;
}
int Pow(int a,int b)
{
	int ans=1;
	for(;b;b>>=1,a=1ll*a*a%mod)
	  if(b&1) ans=1ll*ans*a%mod;
	return ans;
}
Mx Pow(Mx a,int b)
{
	Mx ans=emp;
	for(int i=0;i<=n_;++i)
	  ans.a[i][i]=1;
	if(b>0) for(;b;b>>=1,a=a*a)
	  if(b&1) ans=ans*a;
	return ans;
}
int Inv(int a) {return 1ll*a*inv%mod;}
int main()
{
	scanf("%d%d",&n,&k),inv=Pow(n*(n-1)>>1,mod-2);
	for(int i=1;i<=n;++i)
	  scanf("%d",&A[i]),A[i]?++n1:++n0;
	n_=min(n0,n1);
	int u=0;
	for(int i=1;i<=n0;++i)
	  if(A[i]) ++u;
	b.a[u][0]=1;
	for(int i=0;i<=n_;++i)
	  {
	  	a.a[i][i]=(((Inv(n1*(n1-1)>>1)+Inv(n0*(n0-1)>>1))%mod+Inv((n1-i)*i))%mod+Inv(i*(n0-i)))%mod;
	  	if(i) a.a[i][i-1]=Inv((n1-i+1)*(n0-i+1));
	  	if(i!=n_) a.a[i][i+1]=Inv((i+1)*(i+1));
	  }
	return printf("%d",(Pow(a,k)*b).a[0][0]),0;
}
```

