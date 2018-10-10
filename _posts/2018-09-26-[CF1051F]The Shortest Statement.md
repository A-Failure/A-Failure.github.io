---
layout:     post
title:      "[CF1051F]The Shortest Statement"
date:       2018-09-26
author:     "Dispwnl"
header-img: "img/used/243.jpg"
catalog: false
tags:
    - Dijkstra
    - 倍增
---
## [题目](http://codeforces.com/problemset/problem/1051/F)
### 题目大意

给定一个有$n$个点，$m$条边的无向图，有$q$个询问，每次询问给定两个数$x,y$，求$x$到$y$的最短路

#### $m-n\leq 20$

### Examples
#### input
```
3 3
1 2 3
2 3 1
3 1 5
3
1 2
1 3
2 3
```
#### output
```
3
4
1
```
#### input
```
8 13
1 2 4
2 3 6
3 4 1
4 5 12
5 6 3
6 7 8
7 8 7
1 4 1
1 8 3
2 6 9
2 7 1
4 6 3
6 8 2
8
1 5
1 7
2 3
2 8
3 7
3 4
6 8
7 8
```
#### output
```
7
5
6
7
7
1
2
```
### 题解

![](/img/study/theshortest.png)

好心的出题人特地把条件标出来了qwq

这个条件有什么用呢？

$m$与$n$差不多大小啊……树？

搞出一棵生成树后还有$21$条边，而两点之间的距离肯定不大于ta们在生成树上的距离

如果最短路不在生成树上，ta们的最短路肯定经过了某条非树边

所以对于每条非树边，以ta的两个端点为源点跑最短路，求出到每个点的最短距离

搞询问的时候直接枚举非树边上的点，答案取$min$就好

写堆优化的时候爆int了QAQ

### 代码
```
# include<bits/stdc++.h>
# define LL long long
using namespace std;
const int MAX=1e5+5;
struct p{
	int x,y,fr;
	LL dis;
	bool operator< (const p &a)
	const{
		return dis<a.dis;
	}
}c[MAX<<1],cc[MAX<<1];
struct o{
	int x;
	LL dis;
	bool operator< (const o& a)
	const{
		return dis>a.dis;
	}
};
int n,m,num,num1,q,cnt;
int h[MAX],h1[MAX],d[MAX],fa[MAX],ID[MAX];
LL d1[MAX];
int f[MAX][20];
LL dis[45][MAX];
bool use[MAX];
int read()
{
	int x(0);
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
void add()
{
	int x=read(),y=read(),dis=read();
	c[++num]=(p){h[x],y,x,dis},h[x]=num;
	c[++num]=(p){h[y],x,y,dis},h[y]=num;
}
void add(int x,int y,int dis)
{
	cc[++num1]=(p){h1[x],y,0,dis},h1[x]=num1;
	cc[++num1]=(p){h1[y],x,0,dis},h1[y]=num1;
}
void dfs(int x,int fa=0)
{
	d[x]=d[fa]+1,f[x][0]=fa;
	for(int i=h1[x];i;i=cc[i].x)
	  if(cc[i].y!=fa) d1[cc[i].y]=d1[x]+cc[i].dis,dfs(cc[i].y,x);
}
void init()
{
	for(int i=1;i<=19;++i)
	  for(int j=1;j<=n;++j)
	    f[j][i]=f[f[j][i-1]][i-1];
}
int find(int x)
{
	if(fa[x]!=x) fa[x]=find(fa[x]);
	return fa[x];
}
int LCA(int x,int y)
{
	if(d[x]>d[y]) swap(x,y);
	int D=d[y]-d[x];
	for(int i=0;(1<<i)<=D;++i)
	  if((1<<i)&D) y=f[y][i];
	if(x==y) return x;
	for(int i=19;i>=0;--i)
	  if(f[x][i]!=f[y][i]) x=f[x][i],y=f[y][i];
	return f[x][0];
}
void Dijkstra(LL *d,int x,bool fl)
{
	priority_queue<o> qu;
	d[x]=0,qu.push((o){x,0});
	while(!qu.empty())
	{
		o tt=qu.top();
		qu.pop();
		if(use[tt.x]==fl) continue;
		use[tt.x]=fl;
		for(int i=h[tt.x];i;i=c[i].x)
		  if(use[c[i].y]!=fl&&d[c[i].y]>d[tt.x]+c[i].dis) d[c[i].y]=d[tt.x]+c[i].dis,qu.push((o){c[i].y,d[c[i].y]});
	}
}
void work(int x)
{
	ID[++cnt]=c[x].fr,ID[++cnt]=c[x].y;
}
main()
{
	n=read(),m=read();
	memset(dis,1,sizeof(dis));
	for(int i=1;i<=m;++i,add());
	for(int i=1;i<=n;fa[i]=i,++i);
	for(int i=1,L=0;i<=num;i+=2)
	  {
	  	if(L==n-1) {work(i);continue;}
	  	int r1=find(c[i].fr),r2=find(c[i].y);
	  	if(r1==r2) {work(i);continue;}
	  	++L,add(c[i].fr,c[i].y,c[i].dis),fa[r1]=r2;
	  }
	sort(ID+1,ID+1+cnt);
	int L=unique(ID+1,ID+1+cnt)-ID-1;
	for(int i=1;i<=L;++i)
	  Dijkstra(dis[i],ID[i],i&1);
	dfs(1),init(),q=read();
	for(int i=1,x,y,lca;i<=q;++i)
	  {
	  	x=read(),y=read(),lca=LCA(x,y);
	  	LL ans=d1[x]+d1[y]-2ll*d1[lca];
	  	for(int j=1;j<=L;++j)
	  	  ans=min(ans,dis[j][x]+dis[j][y]);
	  	printf("%lld\n",ans);
	  }
	return 0;
}
```
