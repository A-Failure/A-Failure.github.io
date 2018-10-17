---
layout:         post
title:          "[ZROI78]Last mile of the way"
date:           2018-10-17
author:         "Dispwnl"
header-img:     "img/used/000.jpg"
catalog:    true
tags:
    - 动态规划
    - 启发式合并
---
## [题目](http://www.zhengruioi.com/problem/78)
### 题目描述
小A从仓库里找出了一棵$n$个点的有根树，1号节点为这棵树的根，树上每个节点的权值为$w_i$, 大小为$a_i$。

现在他心中产生了$Q$个疑问，每个疑问形如在$x$的子树里，选出一些大小和不超过$s$的节点(不可以重复选一个节点)，最大权值和可以为多少。

### 输入格式
一行一个整数$n$。

$n - 1$行两个整数$u_i$, $v_i$表示一条边。

$n$行每行两个整数$w_i$, $a_i$表示这个点的权值和大小。

一行一个整数$Q$。

每行两个整数$x$, $s$，表示一个询问。

### 输出格式
$Q$行每行一个整数表示答案

### 样例一
#### input
```
5
1 3
2 4
5 3
4 3
2 3
3 2
1 4
5 4
3 1
7
1 5
2 1 
2 2
2 3
4 4
3 3
3 5
```
#### output
```
8
0
3
3
5
6
8
```
### 限制与约定
$10\%$的数据满足$n \leq 10, s, a_i \leq 10, w_i \leq 10$。

$30\%$的数据满足$n \leq 5000, s, a_i \leq 100, w_i \leq 10^6$。

另外$20\%$的数据满足树是一条链，并且$1$是链的一端。

$100\%$的数据满足$n, s, a_i \leq 5000, w_i \leq 10^6, q \leq 10^5$。

1s, 512MB
### 题解
裸的树上背包！

~~Aufun：这么裸的题你也做？~~

兴冲冲地打了一份代码结果发现复杂度是$O(ns^2)$的？
![](/img/1213.jpg)
$10$分……

我：$Refun$啊，裸的树上背包多少复杂度啊

$Aufun$：$O(nm)$的啊~~你连这个都不知道你退役吧~~

$fuge$：$O(nm)$的啊

$malao$：$O(nm)$的啊~~看我证明~~
![](/img/？？？？？.jpg)

~~完蛋了学假了~~

很好假设ta是$O(ns^2)$的，怎么优化呢？

考虑启发式合并，把最重的儿子的复制过来，其他的与父节点暴力背包合并，每个点合并$O(logn)$次，复杂度就是$O(nslogn)$

### 代码
```
# include<iostream>
# include<cstdio>
# include<cstring>
# include<algorithm>
# define LL long long
# define max(x,y) ((x)>(y)?(x):(y))
using namespace std;
const int MAX=5e3+5;
struct p{
	int x,y;
}c[MAX<<1];
int n,q,num;
int a[MAX],w[MAX],siz[MAX],fa[MAX],h[MAX],Siz[MAX];
LL f[MAX][MAX];
int read()
{
	int x=0,fl=1;
	char ch=getchar();
	for(;!isdigit(ch);fl=(ch=='-')?-1:1,ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x*fl;
}
void Dfs(int x=1,int F=0)
{
	siz[x]=a[x],Siz[x]=1,fa[x]=F;
	for(int i=h[x];i;i=c[i].x)
	  if(c[i].y!=F) Dfs(c[i].y,x),siz[x]+=siz[c[i].y],Siz[x]+=Siz[c[i].y];
}
void merge(int qwq,int x)
{
	for(int i=MAX-5;i>=a[x];--i)
	  f[qwq][i]=max(f[qwq][i],f[qwq][i-a[x]]+w[x]);
	for(int i=h[x];i;i=c[i].x)
	  if(c[i].y!=fa[x]) merge(qwq,c[i].y);
}
void dfs(int x=1)
{
	int maxn=0,son=-1;
	for(int i=h[x];i;i=c[i].x)
	  if(c[i].y!=fa[x])
	  {
	  	if(Siz[c[i].y]>maxn) maxn=Siz[c[i].y],son=c[i].y;
	  	dfs(c[i].y);
	  }
	if(son!=-1) memcpy(f[x],f[son],sizeof(f[x]));
	for(int i=MAX-5;i>=a[x];--i)
	  f[x][i]=max(f[x][i],f[x][i-a[x]]+w[x]);
	for(int i=h[x];i;i=c[i].x)
	  if(c[i].y!=fa[x]&&son!=c[i].y) merge(x,c[i].y);
}
void add()
{
	int x=read(),y=read();
	c[++num]=(p){h[x],y},h[x]=num;
	c[++num]=(p){h[y],x},h[y]=num;
}
int main()
{
	n=read();
	for(int i=1;i<n;++i,add());
	for(int i=1;i<=n;++i)
	  w[i]=read(),a[i]=read();
	Dfs(),dfs();
	q=read();
	for(int i=1,x,s;i<=q;++i)
	  x=read(),s=read(),printf("%lld\n",f[x][min(s,siz[x])]);
	return 0;
}
```
~~后记：Aufun为了证明这个是O(ns)的打了一个连样例都没过的代码最后颓然地表明看错题了哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈~~
