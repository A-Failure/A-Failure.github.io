---
layout:		post
title:		"[CF914E]Palindromes in a Tree"
date:		2019-01-16
author:		Dispwnl
header-img:	"img/used/4452.jpg"
catalog:	false
tags:
    - 点分治
    - 状态压缩
---

## [题目](https://codeforces.com/problemset/problem/914/E)

### 题目大意

给定一棵树，每个点有一个在<code>a</code>和<code>t</code>之间的小写字母，定义回文路径指这条路径上所有字符能组成回文串，求经过每个点的回文路径数量

### Examples

#### input

```plain
5
1 2
2 3
3 4
3 5
abcbb
```

### output

```plain
1 3 4 3 3 
```

### input

```plain
7
6 2
4 3
3 7
5 2
7 2
1 4
afefdfs
```

### output

```plain
1 4 1 1 2 4 2
```

### 题解

根据定义，只要一条路径上出现奇数次的字母数量$\le 1$，这条路径就是回文路径了

因为字符集不超过$19$，所以可以状态压缩表示字母的奇偶性

点分治记录子树中状态为$S$的链有多少条，统计答案可以枚举状态$S\oplus (1<<i)$

统计每个点的答案的时候，可以处理每个子树的信息，对于点$x$，ta某个子树中的某个点$y$能与其他子树中的某个点组成答案，那么$y$到$x$的答案都得$+1$，递归处理即可

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
# define LL long long
using namespace std;
const int MAX=2e6+5,N=1.1e6+5;
struct p{
	int x,y;
}c[MAX<<1];
int n,num,Sum,rt;
int h[MAX],val[MAX],siz[MAX],f[MAX],Num[N];
LL ans[MAX];
char a[MAX];
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
void GET_NUM(int x,int S,int D,int fa=0)
{
	int A=S^(1<<val[x]);
	Num[A]+=D;
	for(int i=h[x];i;i=c[i].x)
	  if((c[i].y^fa)&&!use[c[i].y]) GET_NUM(c[i].y,A,D,x);
}
LL GET_ANS(int x,int S=0,int fa=0)
{
	int A=S^(1<<val[x]);
	LL Ans=Num[A];
	for(int i=0;i<=19;++i)
	  Ans+=Num[A^(1<<i)];
	for(int i=h[x];i;i=c[i].x)
	  if((c[i].y^fa)&&!use[c[i].y]) Ans+=GET_ANS(c[i].y,A,x);
	ans[x]+=Ans;
	return Ans;
}
void GET_ROOT(int x,int fa=0)
{
	f[x]=0,siz[x]=1;
	for(int i=h[x];i;i=c[i].x)
	  if(c[i].y^fa&&!use[c[i].y]) GET_ROOT(c[i].y,x),f[x]=max(f[x],siz[c[i].y]),siz[x]+=siz[c[i].y];
	f[x]=max(f[x],Sum-siz[x]);
	if(f[x]<f[rt]) rt=x;
}
void dfs(int x=rt)
{
	use[x]=1,GET_NUM(x,0,1);
	LL Ans=Num[0];
	for(int i=0;i<=19;++i) Ans+=Num[1<<i];
	for(int i=h[x];i;i=c[i].x)
	  if(!use[c[i].y]) GET_NUM(c[i].y,1<<val[x],-1,x),Ans+=GET_ANS(c[i].y),GET_NUM(c[i].y,1<<val[x],1,x);
	ans[x]+=Ans>>1,GET_NUM(x,0,-1);
	for(int i=h[x];i;i=c[i].x)
	  if(!use[c[i].y]) Sum=f[rt=0]=siz[c[i].y],GET_ROOT(c[i].y,x),dfs(rt);
}
int main()
{
	n=read();
	for(int i=1,x;i<n;++i)
	  x=read(),add(x,read());
	scanf("%s",a+1);
	for(int i=1;i<=n;++i)
	  val[i]=a[i]-'a',ans[i]=1;
	f[0]=Sum=n,GET_ROOT(1),dfs();
	for(int i=1;i<=n;++i)
	  printf("%I64d\n",ans[i]);
	return 0;
}
```

