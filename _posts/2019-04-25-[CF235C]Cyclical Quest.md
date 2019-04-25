---
layout:		post
title:		"[CF235C]Cyclical Quest"
date:		2019-04-25
author:		"Dispwnl"
header-img:	"img/used/14321612.png"
catalog:	false
tags:
    - 后缀自动机
---

## [题目](http://codeforces.com/problemset/problem/235/C)

### 题目大意

给一个主串和多个询问串，求询问串的所有样子不同的周期同构出现次数和

### Examples

#### input

```plain
baabaabaaa
5
a
ba
baa
aabaa
aaba
```

#### output

```plain
7
5
7
3
5
```

#### input

```plain
aabbaa
3
aa
aabb
abba
```

#### output

```plain
2
3
3
```
### 题解

>  写完之后发现以前写过这道题emmmm

破环成链，然后直接在主串的$SAM$上跑就行

暴跳跑的还比倍增快……

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
const int MAX=2e6+5;
int n,Q;
char a[MAX];
struct SAM{
	int l,r,L;
	int len[MAX],fa[MAX],use[MAX],id[MAX],tax[MAX],siz[MAX];
	int son[MAX][26];
	SAM() {l=r=1;}
	void ins(int x)
	{
		int tt=r;
		len[r=++l]=++L,siz[r]=1;
		for(;tt&&!son[tt][x];tt=fa[tt])
		  son[tt][x]=r;
		if(!tt) return void(fa[r]=1);
		int qwq=son[tt][x];
		if(len[qwq]==len[tt]+1) return void(fa[r]=qwq);
		len[++l]=len[tt]+1,fa[l]=fa[qwq],fa[qwq]=fa[r]=l;
		for(int i=0;i<26;++i)
		  son[l][i]=son[qwq][i];
		for(int i=tt;son[i][x]==qwq;i=fa[i])
		  son[i][x]=l;
	}
	void Init()
	{
		for(int i=1;i<=l;++i)
		  ++tax[len[i]];
		for(int i=1;i<=n;++i)
		  tax[i]+=tax[i-1];
		for(int i=l;i>=1;--i)
		  id[tax[len[i]]--]=i;
		for(int i=l;i>=1;--i)
		  siz[fa[id[i]]]+=siz[id[i]];
	}
	int Solve(int _n,int id)
	{
		int ans=0;
		for(int i=1,x=1,_L=0;i<=2*_n;++i)
		  {
		  	if(son[x][a[i]-'a']) x=son[x][a[i]-'a'],++_L;
		  	else
		  	{
		  		while(x&&!son[x][a[i]-'a']) x=fa[x];
		  		if(!x) x=1,_L=0;
		  		else _L=len[x]+1,x=son[x][a[i]-'a'];
			}
		  	while(x&&len[fa[x]]>=_n) _L=len[x=fa[x]];
		  	if(_L>=_n&&use[x]!=id) use[x]=id,ans+=siz[x]; 
		  }
		return ans;
	}
}_S;
int main()
{
	scanf("%s%d",a+1,&Q),n=strlen(a+1);
	for(int i=1;i<=n;++i)
	  _S.ins(a[i]-'a');
	_S.Init();
	for(int i=1,_n;i<=Q;++i)
	  {
	  	scanf("%s",a+1),_n=strlen(a+1);
	  	for(int j=1;j<=_n;++j)
	  	  a[j+_n]=a[j];
	  	printf("%d\n",_S.Solve(_n,i));
	  }
	return 0;
}
```

