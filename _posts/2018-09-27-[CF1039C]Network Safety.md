---
layout:     post
title:      "[CF1039C]Network Safety"
date:       2018-09-27
author:     "Dispwnl"
header-img: "img/used/56742.jpg"
catalog: false
tags:
    - 并查集
---
## [题目](http://codeforces.com/problemset/problem/1039/C)
### 题目大意
给定一个$n$个点$m$条边的图，每个点都有一个权值$c_i$

定义图为安全的条件为对于所有的边$(u,v)$都保证$c_u≠c_v$

求对于任意的$x$，集合$s$中所有点的点权$xor~x$后图仍然安全，这样的$x$和$s$的组合的数量

### Examples
#### input
```
4 4 2
0 1 0 1
1 2
2 3
3 4
4 1
```
#### output
```
50
```
#### input
```
4 5 3
7 1 7 2
1 2
2 3
3 4
4 1
2 4
```
#### output
```
96
```
### 题解
发现产生影响的$x$最多有$m$个取值，即$c_{ui}~xor~c_{vi}$

其他的$x$的贡献都是$2^n$

对于产生影响的$x$，对于$c_{u_i}~xor~c_{v_i}=x$的边，$u_i$与$v_i$不能放在一个集合里

所以枚举每条边，把不能放一个集合里的点合并成一个点处理即可

### 代码
```
# include<bits/stdc++.h>
# define LL long long
using namespace std;
const int MAX=5e5+1,mod=1e9+7;
struct p{
	int x,y;
	LL _xor;
	bool operator< (const p &a)
	const{
		return _xor<a._xor;
	}
}c[MAX];
int n,m,k;
LL ans;
int fa[MAX];
LL C[MAX];
LL read()
{
	LL x(0);
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
LL Pow(LL b)
{
	LL ans=1,a=2;
	for(;b;b>>=1,a=a*a%mod)
	  if(b&1) ans=ans*a%mod;
	return ans;
}
int find(int x)
{
	if(fa[x]!=x) fa[x]=find(fa[x]);
	return fa[x];
}
int main()
{
	n=read(),m=read(),k=read();
	for(int i=1;i<=n;++i)
	  C[i]=read(),fa[i]=i;
	for(int i=1;i<=m;++i)
	  c[i].x=read(),c[i].y=read(),c[i]._xor=C[c[i].x]^C[c[i].y];
	sort(c+1,c+1+m);
	int L=1,Use=0,siz;
	for(int i=2;i<=m;++i)
	  if(c[i]._xor!=c[i-1]._xor)
	  {
	  	++Use,siz=n;
	  	for(int j=L;j<i;++j)
	  	  {
	  	  	int r1=find(c[j].x),r2=find(c[j].y);
	  	  	if(r1!=r2) --siz,fa[r1]=r2;
		  }
		ans=(ans+Pow(siz))%mod;
		for(int j=L;j<i;++j)
		  fa[c[j].x]=c[j].x,fa[c[j].y]=c[j].y;
		L=i;
	  }
	++Use,siz=n;
	for(int i=L;i<=m;++i)
	  {
	  	int r1=find(c[i].x),r2=find(c[i].y);
	  	if(r1!=r2) --siz,fa[r1]=r2;
	  }
	return printf("%lld",(ans+(Pow(k)-Use+mod)%mod*Pow(n)%mod+Pow(siz)%mod)%mod),0;
}
```
