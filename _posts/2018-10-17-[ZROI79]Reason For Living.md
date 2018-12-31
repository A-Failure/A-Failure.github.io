---
layout:         post
title:          "[ZROI79]Reason For Living"
date:           2018-10-17
author:         "Dispwnl"
header-img:     "img/used/902.jpg"
catalog:    true
tags:
    - 二分答案
    - 并查集
---
## [题目](http://www.zhengruioi.com/problem/79)
### 题目描述
小B准备设计施工方案。

设计图是一个$n$个点$m$条边的图，小B每次施工可以取图中一个还没有完工的生成森林把它完工。

为了加快施工效率，每次取的时候小B都会最大化当前这个生成森林的边数。请你帮他找出一个符合要求的施工方案。

如果有多个方案，输出任意一种即可。

### 输入格式
第一行两个整数$n$, $m$。

后面$m$行，每行两个数$a_i, b_i$表示一条边，保证没有自环。

### 输出格式
$m$行，每行一个整数，表示这条边属于第几次。如果有多个方案，输出任意一种即可。

### 样例一
#### input
```
5 7
1 2
2 3
3 4
4 5
1 2
2 3
1 2
```
#### output
```
1
1
1
1
3
2
2
```
### 限制与约定
$20\%$的分数满足$n \leq 1000, m \leq 1000$。

$40\%$的分数满足$n \leq 10^4, m \leq 10^4$。

$80\%$的分数满足$n \leq 10^5, m \leq 10^5$。

$100\%$的分数满足$n \leq 10^6, m \leq 10^6$。

1s, 512MB

### 题解
想到了分层处理并查集却~~懒得~~没有实现XD……

发现一个点的最大可能层数是ta的度

所以每条边二分在哪一层（枚举会<code>TLE</code>），每次连一下并查集就好了

因为数据范围是$10^6$，所以用<code>vector</code>维护每一层的并查集

### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<vector>
# include<algorithm> 
using namespace std;
const int MAX=1e6+5;
struct p{
	int x,y;
}c[MAX];
int n,m;
int du[MAX];
vector<int> fa[MAX];
int read()
{
	int x(0);
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
int find(int c,int x)
{
	if(fa[x][c]!=x) fa[x][c]=find(c,fa[x][c]);
	return fa[x][c];
}
int main()
{
	n=read(),m=read();
	for(int i=1;i<=m;++i)
	  ++du[c[i].x=read()],++du[c[i].y=read()];
	for(int i=1;i<=n;du[i++]=0)
	  {
	  	fa[i].resize(du[i]);
	  	for(int j=0;j<du[i];++j)
	  	  fa[i][j]=i;
	  }
	for(int i=1,x,y,l,r,ans;i<=m;++i)
	  {
	  	x=c[i].x,y=c[i].y,l=0,r=min(++du[x],++du[y])-1;
	  	while(l<=r)
	  	{
	  		int mid(l+r>>1);
	  		if(find(mid,x)!=find(mid,y)) r=mid-1,ans=mid;
	  		else l=mid+1;
		}
		printf("%d\n",ans+1),fa[find(ans,x)][ans]=find(ans,y);
	  }
	return 0;
}
```
