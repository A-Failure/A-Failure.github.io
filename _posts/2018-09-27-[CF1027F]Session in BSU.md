---
layout:     post
title:      "[CF1027F]Session in BSU"
date:       2018-09-27
author:     "Dispwnl"
header-img: "img/used/124.jpg"
catalog:    false
tags:
    - 并查集
---
## [题目](http://codeforces.com/contest/1027/problem/F)
### 题目大意
有$n$场考试，每一场必须在第$a_i$或$b_i$天完成，问最早能在第几天完成所有考试

### Examples
#### input
```
2
1 5
1 7
```
#### output
```
5
```
#### input
```
3
5 13
1 5
1 7
```
#### output
```
7
```
#### input
```
3
10 40
40 80
10 80
```
#### output
```
80
```
#### input
```
3
99 100
99 100
99 100
```
#### output
```
-1
```

### 题解
先开始想拿二分+网络流卡过去的，结果完美的TLE了……

把每次考试的$a,b$之间连边，这时候每条边代表一场考试

对于每个联通块有三种情况：

- 边数大于点数，无论如何都匹配不完，就是无解的情况
- 边数等于点数，这样就形成了一棵基环树，每条边都能找到对应的点匹配，所以答案就是最大的点
- 边数小于点数，即边数等于点数$-1$，这时形成了一棵树，有一个点能不被匹配上，贪心选的话答案就是次大的点

还不懂的话可以画画图理解一下qwq

用并查集维护最大值次大值即可，并在合并过程中判断是否有合法答案

### 代码
```
# include<bits/stdc++.h>
using namespace std;
const int MAX=2e6+5;
struct p{
	int x,id,fl;
	bool operator< (const p &a)
	const{
		return x<a.x;
	}
}a[MAX];
int n,id,ans,cnt;
int L[MAX],R[MAX],h[MAX],ID[MAX],fa[MAX],maxn[MAX],_maxn[MAX];
bool use[MAX];
int read()
{
	int x(0);
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
int find(int x)
{
	if(fa[x]!=x) fa[x]=find(fa[x]);
	return fa[x];
}
int main()
{
	n=read();
	for(int i=1;i<=n;++i)
	  a[++cnt]=(p){read(),i,0},a[++cnt]=(p){read(),i,1};
	sort(a+1,a+1+cnt);
	for(int i=1;i<=cnt;++i)
	  {
	  	if(a[i].x!=a[i-1].x) ++id;
	  	ID[id]=a[i].x;
	  	if(a[i].fl) R[a[i].id]=id;
	  	else L[a[i].id]=id;
	  }
	for(int i=1;i<=id;++i)
	  fa[i]=i,maxn[i]=i;
	for(int i=1;i<=n;++i)
	  {
	  	int r1=find(L[i]),r2=find(R[i]);
	  	if(r1!=r2)
	  	{
	  		fa[r1]=r2,_maxn[r2]=max(_maxn[r2],_maxn[r1]);
	  		if(use[r1]) use[r2]=1;
	  		if(maxn[r1]>maxn[r2]) _maxn[r2]=max(_maxn[r2],maxn[r2]);
	  		else if(maxn[r1]<maxn[r2]) _maxn[r2]=max(_maxn[r2],maxn[r1]);
	  		maxn[r2]=max(maxn[r1],maxn[r2]);
		}
		else if(!use[r2]) use[r2]=1;
		else return printf("-1"),0;
	  }
	for(int i=1;i<=id;++i)
	  if(fa[i]==i) if(use[i]) ans=max(ans,maxn[i]);
	  else ans=max(ans,_maxn[i]);
	return printf("%d",ID[ans]),0;
}
```
