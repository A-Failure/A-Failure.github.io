---
layout:		post
title:		"[CF710F]String Set Queries"
date:		2019-02-21
author:		"Dispwnl"
header-img:	"img/used/00213.png"
catalog:	false
tags
    - AC自动机
    - 二进制分组
---

## [题目](http://codeforces.com/problemset/problem/710/F)

### 题目大意

维护一个集合，有三种操作：

- 插入一个字符串
- 删除一个字符串
- 给定一个字符串$a$，求现在集合中的字符串在$a$中出现了多少次

### Examples

#### input

```plain
5
1 abc
3 abcabc
2 abc
1 aba
3 abababc
```

#### output

```plain
2
2
```

#### input

```plain
10
1 abc
1 bcd
1 abcd
3 abcd
2 abcd
3 abcd
2 bcd
3 abcd
2 abc
3 abcd
```

output

```plain
3
2
1
0
```
### 题解

$AC$自动机不支持删除操作，所以可以开两个$AC$自动机，一个存插入字符串，一个存删除字符串，答案就是查询字符串在两个自动机上跑出来的答案之差

但是要是插入一个就处理一次$fail$指针肯定会<code>TLE</code>

考虑二进制分组，每次合并$Trie$，这里就需要保留每个区间$Trie$的结构，不能直接建$Trie$图

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<vector>
# include<algorithm>
# define LL long long
using namespace std;
const int MAX=3e5+5;
int n;
int qu[MAX];
char b[MAX];
struct AC{
	int tot,Top,Siz;
	int st[MAX],fail[MAX],val[MAX],Val[MAX],rt[21],siz[21];
	int son[MAX][26],_son[MAX][26];
	AC() {tot=Top=Siz=0;}
	int GET_POINT() {return Top?st[Top--]:++tot;}
	void merge(int &x,int &y)
	{
		if(!x||!y) return void(x+=y);
		st[++Top]=y;
		for(int i=0;i<26;++i)
		  merge(son[x][i],son[y][i]),son[y][i]=_son[y][i]=0;
		Val[x]+=Val[y],val[y]=Val[y]=fail[y]=0;
	}
	void ins(int k,int _L)
	{
		int x=rt[k];
		for(int i=1,d;i<=_L;++i)
		  if(son[x][b[i]-'a']) x=son[x][b[i]-'a'];
		  else d=son[x][b[i]-'a']=GET_POINT(),x=d;
		++Val[x];
	}
	void GET_FAIL(int k)
	{
		int hl=1,tl=0;
		for(int i=0;i<26;++i)
		  if(son[rt[k]][i]) _son[rt[k]][i]=qu[++tl]=son[rt[k]][i],fail[son[rt[k]][i]]=rt[k];
		  else _son[rt[k]][i]=rt[k];
		while(hl<=tl)
		{
			int tt=qu[hl++];
			for(int i=0;i<26;++i)
			  if(son[tt][i]) fail[_son[tt][i]=son[tt][i]]=_son[fail[tt]][i],qu[++tl]=son[tt][i];
			  else _son[tt][i]=_son[fail[tt]][i];
			val[tt]=Val[tt]+val[fail[tt]]; 
		}
	}
	LL ask(int _L)
	{
		LL ans=0;
		for(int i=1;i<=Siz;++i)
		  for(int j=1,x=rt[i];j<=_L;++j)
			x=_son[x][b[j]-'a'],ans+=val[x];
		return ans;
	}
	void Ins(int _L)
	{
		siz[++Siz]=1,rt[Siz]=GET_POINT(),ins(Siz,_L);
		while(Siz>1&&siz[Siz]==siz[Siz-1]) merge(rt[Siz-1],rt[Siz]),siz[Siz-1]<<=1,--Siz;
		GET_FAIL(Siz);
	}
}_A,__A;
int main()
{
	scanf("%d",&n);
	for(int i=1,op,_n;i<=n;++i)
	  {
	  	scanf("%d%s",&op,b+1),_n=strlen(b+1);
	  	if(op==1) _A.Ins(_n);
	  	else if(op==2) __A.Ins(_n);
	  	else if(op==3) printf("%I64d\n",_A.ask(_n)-__A.ask(_n)),fflush(stdout);
	  }
	return 0;
}
```

