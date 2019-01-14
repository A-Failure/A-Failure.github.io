---
layout:		post
title:		"[CF700E]Cool Slogans"
date:		2019-01-14
author:		"Dispwnl"
header-img:	"img/used/568568.jpg"
catalog:	false
tags:
    - 后缀自动机
    - 线段树
    - 动态规划
---

## [题目](https://codeforces.com/problemset/problem/700/E)

### 题目大意

给定一个字符串$S$，现在要找$S$中的一个子串$T$，找到$T$中出现过不少于两次的子串$A$，使得$T=A$，然后重复这个操作

求最多能执行多少次

### Examples

#### input

```plain
3
abc
```

#### output

```plain
1
```

#### input

```plain
5
ddddd
```

#### output


```plain
5
```

#### input

```plain
11
abracadabra
```

#### output

```plain
3
```

### 题解

假设已经知道$T$是哪个状态（姑且定为$t$）了，那么接下来找的$A$的状态$a$肯定是后缀树$t$的某个祖先

所以查询转移到$t$的父亲的状态$t'$子树里$[endpos_t-len_t+len_{t'},endpos_t-1]$中是否存在节点，有的话说明可以转移，否则说明$t$可以替换$t$的父亲（长度增加）

从上到下转移下来，中间的最大值就是答案

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
# define ID id[i]
# define tl s[k].l
# define tr s[k].r
# define mid (l+r>>1)
using namespace std;
const int MAX=5e5+10;
struct p{
	int l,r;
}s[MAX<<5];
int n,tot,ans;
int rt[MAX];
char a[MAX];
void change(int l,int r,int &k,int x)
{
	if(!k) k=++tot;
	if(l==r) return;
	if(x<=mid) change(l,mid,tl,x);
	else change(mid+1,r,tr,x);
}
int merge(int x,int y)
{
	if(!x||!y) return x+y;
	int k=++tot;
	return tl=merge(s[x].l,s[y].l),tr=merge(s[x].r,s[y].r),k;
}
bool ask(int l,int r,int k,int L,int R)
{
	if(!k||r<L||R<l) return 0;
	if(l>=L&&r<=R) return 1;
	if(ask(l,mid,tl,L,R)) return 1;
	return ask(mid+1,r,tr,L,R);
}
struct SAM{
	int l,r,L;
	int len[MAX],fa[MAX],pos[MAX],f[MAX],id[MAX],tax[MAX],_fa[MAX];
	int son[MAX][26];
	SAM() {l=r=1;}
	void ins(int x,int id)
	{
		int tt=r;
		len[r=++l]=++L,change(1,n,rt[r],pos[r]=id);
		for(;tt&&!son[tt][x];tt=fa[tt])
		  son[tt][x]=r;
		if(!tt) return void(fa[r]=1);
		int qwq=son[tt][x];
		if(len[qwq]==len[tt]+1) return void(fa[r]=qwq);
		len[++l]=len[tt]+1,fa[l]=fa[qwq],fa[qwq]=fa[r]=l,change(1,n,rt[l],pos[l]=id);
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
		  if(fa[ID]) rt[fa[ID]]=merge(rt[fa[ID]],rt[ID]);
	}
	void Solve()
	{
		Init();
		for(int i=1;i<=l;++i)
		  {
		  	if(fa[ID]==1) _fa[ID]=ID,f[ID]=1;
		  	else if(ask(1,n,rt[_fa[fa[ID]]],pos[ID]-len[ID]+len[_fa[fa[ID]]],pos[ID]-1)) f[ID]=f[_fa[fa[ID]]]+1,_fa[ID]=ID;
		  	else f[ID]=f[fa[ID]],_fa[ID]=_fa[fa[ID]];
		  	ans=max(ans,f[ID]);
		  }
		printf("%d",ans);
	}
}_S;
int main()
{
	scanf("%d%s",&n,a+1);
	for(int i=1;i<=n;++i)
	  _S.ins(a[i]-'a',i);
	return _S.Solve(),0;
}
```

