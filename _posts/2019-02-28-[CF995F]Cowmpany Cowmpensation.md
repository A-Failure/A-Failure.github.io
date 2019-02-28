---
layout:		post
title:		"[CF995F]Cowmpany Cowmpensation"
date:		2019-02-28
author:		"Dispwnl"
header-img:	"img/used/"
catalog:	false
tags:
    - 拉格朗日插值
    - 动态规划
---

## [题目](http://codeforces.com/problemset/problem/995/F)

### 题目大意

给定一棵树，每个点要确定一个$[1,D]$之间的权值，要求每个点（除$1$节点，即根）的权值不大于它的父亲

求分配方案数

### Examples

#### Input

```plain
3 2
1
1
```

#### Output

```plain
5
```

#### Input

```plain
3 3
1
2
```

#### Output

```plain
10
```

#### Input

```plain
2 5
1
```

#### Output

```plain
15
```

### 题解

如果$D$很小的话简单$dp$，设$f_{i,j}$表示点$i$确定权值为$j$的方案数，然后每个点子树向上转移，维护$f_i$的前缀和就好

但是$D$是$1e9$……

发现叶子节点的$f_i$可以看成一个一次函数，往上的时候是子树组合相乘出来的，相当于一个最高次为子树叶子节点数的多项式（？）

这样就可以用拉格朗日插值优化了，注意预先把逆元处理出来以减小复杂度

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
const int N=3e3+5,mod=1e9+7;
struct p{
	int x,y;
}c[N];
int n,D,num;
int h[N],w[N];
int f[N][N];
int read()
{
	int x(0);
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
void add(int x,int y) {c[++num]=(p){h[x],y},h[x]=num;}
void dfs(int x=1)
{
	for(int i=1;i<=n;++i)
	  f[x][i]=1;
	for(int i=h[x];i;i=c[i].x)
	  {
	  	dfs(c[i].y);
	  	for(int j=1;j<=n;++j)
	  	  f[x][j]=1ll*f[x][j]*f[c[i].y][j]%mod;
	  }
	for(int i=1;i<=n;++i)
	  {
	  	f[x][i]+=f[x][i-1];
	  	if(f[x][i]>=mod) f[x][i]-=mod;
	  }
}
int Pow(int a,int b)
{
	int ans=1;
	for(;b;b>>=1,a=1ll*a*a%mod)
	  if(b&1) ans=1ll*ans*a%mod;
	return ans;
}
int L(int x)
{
	int ans=1;
	for(int i=0;i<=n;++i)
	  if(x!=i) ans=(1ll*ans*(D-i)%mod*(x-i<0?-1:1)*w[x-i<0?i-x:x-i]%mod+mod)%mod;
	return ans;
}
void Solve()
{
	if(n>=D) return void(printf("%d",f[1][D]));
	int ans=0;
	for(int i=1;i<=n;++i)
	  w[i]=Pow(i,mod-2);
	for(int i=0;i<=n;++i)
	  {
	  	ans+=1ll*f[1][i]*L(i)%mod;
	  	if(ans>=mod) ans-=mod;
	  }
	printf("%d",ans);
}
int main()
{
	n=read(),D=read();
	for(int i=2;i<=n;++i)
	  add(read(),i);
	return dfs(),Solve(),0;
}
```

