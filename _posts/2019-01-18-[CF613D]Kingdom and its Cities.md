---
layout:		post
title:		"[CF613D]Kingdom and its Cities"
date:		2019-01-18
author:		"Dispwnl"
header-img:	"img/used/133.jpg"
catalog:	false
tags:
    - 虚树
    - 动态规划

---

## [题目](https://codeforces.com/contest/613/problem/D)

### 题目大意

给定一颗$n$个点的树，每次询问选择$k$个关键点，问至少选多少个非关键点才能使关键点两两不连通

### Examples

#### input

```plain
4
1 3
2 3
4 3
4
2 1 2
3 2 3 4
3 1 2 4
4 1 2 3 4
```

#### output

```plain
1
-1
1
-1
```

#### input

```plain
7
1 2
2 3
3 4
1 5
5 6
5 7
1
4 2 4 6 7
```

#### output

```plain
2
```

### 题解

首先关键点如果有父子关系肯定就搞不出来

先建出虚树来，用$f_i$表示$i$节点子树中处理好用的最小点数，$g_i$表示$i$子树中未解决的点数

对于非关键点，最优策略肯定是如果能不选就不选，能晚解决就晚解决

对于关键点$x$，无论怎么处理，未解决的点最小肯定是$1$（即$x$），并且肯定要每个没解决的点都要用一个点去截断（防止与$x​$匹配）

对于非关键点$x$，如果未解决的点数超过$1$个，说明ta们可跨过$x$进行匹配，这时$x$必须要选，同时所有的点都解决了；否则可以不选$x​$，未解决的点不变

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
const int MAX=1e5+5;
struct p{
	int x,y;
}c[MAX<<1];
int n,Q,num,ans,Top,cnt;
int h[MAX],T[MAX],siz[MAX],fa[MAX],son[MAX],top[MAX],st[MAX],id[MAX],d[MAX];
int f[2][MAX];
bool use[MAX];
int read()
{
	int x(0);
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
void add(int x,int y)
{
	c[++num]=(p){h[x],y},h[x]=num;
	c[++num]=(p){h[y],x},h[y]=num;
}
bool cmp(int a,int b) {return id[a]<id[b];}
void dfs(int x=1,int F=0)
{
	fa[x]=F,d[x]=d[F]+(siz[x]=1);
	for(int i=h[x];i;i=c[i].x)
	  if(c[i].y^F)
	  {
	  	dfs(c[i].y,x),siz[x]+=siz[c[i].y];
	  	if(siz[c[i].y]>siz[son[x]]) son[x]=c[i].y;
	  }
}
void dfs1(int x=1,int tp=1)
{
	top[x]=tp,id[x]=++cnt;
	if(!son[x]) return void(h[x]=0);
	dfs1(son[x],tp);
	for(int i=h[x];i;i=c[i].x)
	  if((c[i].y^son[x])&&(c[i].y^fa[x])) dfs1(c[i].y,c[i].y);
	h[x]=0;
}
int LCA(int x,int y)
{
	while(top[x]^top[y])
	{
		if(d[top[x]]<d[top[y]]) swap(x,y);
		x=fa[top[x]];
	}
	return d[x]>d[y]?y:x;
}
int GET_DIS(int x,int y) {return d[x]+d[y]-(d[LCA(x,y)]<<1);}
void Dfs(int x=1,int fa=0)
{
	f[0][x]=f[1][x]=0;
	for(int i=h[x];i;i=c[i].x)
	  if(c[i].y^fa) Dfs(c[i].y,x),f[0][x]+=f[0][c[i].y],f[1][x]+=f[1][c[i].y];
	if(use[x]) f[0][x]+=f[1][x],f[1][x]=1;
	else if(f[1][x]>1) ++f[0][x],f[1][x]=0;
	h[x]=0;
}
void Work(int k)
{
	num=ans=0,st[Top=1]=1;
	for(int i=1;i<=k;++i)
	  if(use[fa[T[i]]]) return void(printf("-1\n"));
	for(int i=1,lca;i<=k;++i)
	  {
	  	lca=LCA(T[i],st[Top]);
	  	while(Top>1&&d[lca]<d[st[Top-1]]) add(st[Top],st[Top-1]),--Top;
	  	while(Top&&d[lca]<d[st[Top]]) add(lca,st[Top]),--Top;
	  	if(!Top||d[lca]>d[st[Top]]) st[++Top]=lca;
	  	if(st[Top]!=T[i]) st[++Top]=T[i];
	  }
	while(Top>1) add(st[Top],st[Top-1]),--Top;
	Dfs(),printf("%d\n",f[0][1]);
}
int main()
{
	n=read();
	for(int i=1,x;i<n;++i)
	  x=read(),add(x,read());
	dfs(),dfs1(),Q=read();
	for(int i=1,k;i<=Q;++i)
	  {
	  	k=read();
	  	for(int j=1;j<=k;++j)
	  	  use[T[j]=read()]=1;
	  	sort(T+1,T+1+k,cmp),Work(k);
	  	for(int j=1;j<=k;++j)
	  	  use[T[j]]=0;
	  }
	return 0;
}
```

