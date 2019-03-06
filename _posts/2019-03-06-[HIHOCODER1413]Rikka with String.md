---
layout:		post
title:		"[HIHOCODER1413]Rikka with String"
date:		2019-03-06
author:		"Dispwnl"
header-img:	"img/used/7867.jpg"
catalog:	false
tags:
    - 后缀自动机
---

## [题目](https://hihocoder.com/problemset/problem/1413)

### 描述

众所周知，萌萌哒六花不擅长数学，所以勇太给了她一些数学问题做练习，其中有一道是这样的：

勇太有一个长度为n的只包含小写字母的字符串s，现在他可以选取一个位置k ∈ [1,n]并把$s_k$上的字符替换成#。

现在他想要对每一个k ∈ [1,n]，知道修改后的字符串中本质不同的子串个数。

当然，这个问题对于萌萌哒六花来说实在是太难了，你可以帮帮她吗？

### 输入

第一行输入一个正整数n。

第二行输入一个长度为n的只包含小写字母的字符串。

$n ≤ 3 × 10^5$

### 输出

输出一行n个整数表示答案。

#### 样例输入

```plain
5
abcab
```

#### 样例输出

```plain
14 14 12 14 14
```

### 题解

首先发现包含<code>#</code>的子串肯定只出现过一次，所以第$i$位上的答案先加上$i(n-i+1)$

所以现在要统计的就是左右两边只出现过一次的子串个数

记录一下每个$right$集合的最左/右出现位置，发现直接统计非常麻烦~~讨论一上午没讨论出来~~，所以考虑统计被在$i$位置的<code>#</code>隔断的本质不同的子串个数，这样用总的本质不同的子串个数一减就是答案

这样只用讨论两种情况即可

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
# define LL long long
using namespace std;
const int MAX=6e5+5;
int n;
LL Ans;
LL ans[MAX];
char a[MAX];
void Upt(int l,int r,int w,int d=0)
{
	if(l>r) return;
	ans[l]+=w,ans[l+1]+=d-w,ans[r+1]-=w+(r-l+1)*d,ans[r+2]+=w+(r-l)*d;
}
struct SAM{
	int l,r,L;
	int fa[MAX],len[MAX],pos_l[MAX],pos_r[MAX],tax[MAX],id[MAX];
	int son[MAX][26];
	SAM() {l=r=1,pos_l[1]=1e5;}
	void ins(int x,int id)
	{
		int tt=r;
		len[r=++l]=++L,pos_l[r]=pos_r[r]=id;
		for(;tt&&!son[tt][x];tt=fa[tt])
		  son[tt][x]=r;
		if(!tt) return void(fa[r]=1);
		int qwq=son[tt][x];
		if(len[qwq]==len[tt]+1) return void(fa[r]=qwq);
		len[++l]=len[tt]+1,fa[l]=fa[qwq],fa[r]=fa[qwq]=l,pos_l[l]=pos_r[l]=id;
		for(int i=0;i<26;++i)
		  son[l][i]=son[qwq][i];
		for(int i=tt;son[i][x]==qwq;i=fa[i])
		  son[i][x]=l; 
	}
	void Init()
	{
		for(int i=1;i<=l;++i)
		  ++tax[len[i]],Ans+=len[i]-len[fa[i]];
		for(int i=1;i<=n;++i)
		  tax[i]+=tax[i-1];
		for(int i=l;i>=1;--i)
		  id[tax[len[i]]--]=i;
		for(int i=l;i>=1;--i)
		  pos_l[fa[id[i]]]=min(pos_l[fa[id[i]]],pos_l[id[i]]),pos_r[fa[id[i]]]=max(pos_r[fa[id[i]]],pos_r[id[i]]);
		for(int i=l;i>=1;--i)
		  Upt(pos_r[id[i]]-len[fa[id[i]]]+1,pos_l[id[i]],min(pos_r[id[i]]-len[fa[id[i]]],pos_l[id[i]])-(pos_r[id[i]]-len[id[i]])),Upt(pos_r[id[i]]-len[id[i]]+1,min(pos_r[id[i]]-len[fa[id[i]]],pos_l[id[i]]),1,1);
	}
}_S;
int main()
{
	scanf("%d%s",&n,a+1);
	for(int i=1;i<=n;++i)
	  _S.ins(a[i]-'a',i);
	_S.Init();
	for(int i=1;i<=n;++i)
	  ans[i]+=ans[i-1];
	for(int i=1;i<=n;++i)
	  ans[i]+=ans[i-1],printf("%lld ",Ans-ans[i]+1ll*i*(n-i+1));
	return 0;
}
```

