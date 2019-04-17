---
layout:		post
title:		"[CEOI2017]Building Bridges"
date:		2019-04-17
author:		"Dispwnl"
header-img:	"img/used"
catalog:	false
tags:
    - 斜率优化
    - 动态规划
    - CDQ分治
---

## [题目](<https://www.luogu.org/problemnew/show/P4655>)

### 题解

首先先推出$O(n^2)dp$，设$f_i$表示连通$1\sim i$需要的最小代价

$f_i=\min(f_j+(h_i-h_j)^2+\sum_{k=j}^{i-1}w_k),j<i$

发现似乎可以斜率优化！拆开式子，$sum$为$w$的前缀和

$f_i=f_j+{h_i}^2+{h_j}^2-2h_ih_j+sum_{i-1}-sum_j$

$f_i-sum_{i-1}-{h_i}^2=-2h_ih_j+f_j+{h_j}^2-sum_j​$

设$x=h_j,k=2h_j,b=f_i-sum_{i-1}-{h_i}^2,y=f_j+{h_j}^2-sum_j$，化成$b=kx+y$的形式了

发现$x$并不连续，所以用$CDQ$分治维护下凸壳优化斜率即可，复杂度$O(n{\log n}^2)$？

不是很会写……

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
# define LL long long
# define mid (l+r>>1)
using namespace std;
const int MAX=1e5+5;
int n,Top;
int h[MAX],w[MAX],id[MAX],st[MAX];
LL sum[MAX],f[MAX];
int read()
{
	int x=0,fl=1;
	char ch=getchar();
	for(;!isdigit(ch);fl=(ch=='-')?-1:1,ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x*fl;
}
LL Y(int x) {return f[x]-sum[x]+1ll*h[x]*h[x];}
int X(int x) {return h[x];}
bool cmp(int a,int b) {return (X(a)!=X(b))?(X(a)<X(b)):(Y(a)<Y(b));}
bool cmp1(int a,int b) {return h[a]<h[b];}
bool Calc(int a,int b,int c) {return (Y(b)-Y(a))*(X(c)-X(b))>=(Y(c)-Y(b))*(X(b)-X(a));}
double calc(int a,int b)
{
	if(X(b)==X(a)) return Y(b)>Y(a)?1e18:-1e18;
	return double(Y(b)-Y(a))/double(X(b)-X(a));
}
void Solve(int l=1,int r=n)
{
	if(l==r) return void(f[l]=(l==1?0:f[l]+1ll*h[l]*h[l]+sum[l-1]));
	Solve(l,mid),sort(id+l,id+mid+1,cmp),sort(id+mid+1,id+r+1,cmp1),Top=0;
	for(int i=l;i<=mid;++i)
	  {
	  	while(Top>1&&Calc(st[Top-1],st[Top],id[i])) --Top;
	  	st[++Top]=id[i];
	  }
	int L=1;
	for(int i=mid+1;i<=r;++i)
	  {
	  	while(L<Top&&calc(st[L],st[L+1])<2*h[id[i]]) ++L;
	  	f[id[i]]=min(f[id[i]],Y(st[L])-2ll*h[id[i]]*X(st[L]));
	  }
	sort(id+mid+1,id+r+1),Solve(mid+1,r);
}
int main()
{
	n=read();
	for(int i=1;i<=n;++i)
	  h[i]=read();
	for(int i=1;i<=n;++i)
	  w[i]=read(),sum[i]=sum[i-1]+w[i],id[i]=i;
	memset(f,1,sizeof(f));
	return Solve(),printf("%lld",f[n]),0;
}
```

