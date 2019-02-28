---
layout:		post
title:		"[HDU5909]Tree Cutting"
date:		2019-02-28
author:		"Dispwnl"
header-img:	"img/used/346346.jpg"
catalog:	false
tags:
    - FWT
---

## [题目](https://vjudge.net/problem/HDU-5909)

### 题目大意

给定一棵树，定义一棵树的权值为每个节点权值的异或和，求这棵树所有子图的权值，输出每种权值的出现次数

### Sample Input

```plain
2
4 4
2 0 1 3
1 2
1 3
1 4
4 4
0 1 3 1
1 2
1 3
1 4
```

### Sample Output

```plain
3 3 2 3
2 4 2 3
```

### 题解

假设$f_{i,j}$表示以$i$为根的子树包含$i$的子图权值为$j$的个数，如果要转移的话就是子树两两之间相互组合，如果$v$是$u$的一棵子树，有$f_{u,i}=\sum_{j\oplus k=i}f_{u,j}\times f_{v,k}$，$FWT$优化即可

注意$v$不与其他子树组合的情况也要统计

开始<code>PE</code>可还行……

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
const int N=1.2e3+5,mod=1e9+7,inv2=5e8+4;
struct p{
	int x,y;
}c[N<<1];
int T,n,lim,num;
int h[N],val[N];
int a[N][N];
void add(int x,int y)
{
	c[++num]=(p){h[x],y},h[x]=num;
	c[++num]=(p){h[y],x},h[y]=num;
}
void FWT_XOR(int *A,int tt=1)
{
	for(int i=1;i<lim;i<<=1)
	  for(int l=i<<1,j=0;j<lim;j+=l)
	    for(int k=0,x,y;k<i;++k)
	      {
	      	x=A[j+k],y=A[i+j+k],A[j+k]=(x+y)%mod,A[i+j+k]=(x-y+mod)%mod;
	      	if(tt==-1) A[j+k]=1ll*A[j+k]*inv2%mod,A[i+j+k]=1ll*A[i+j+k]*inv2%mod;
		  }
}
int read()
{
	int x(0);
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
void dfs(int x=1,int fa=0)
{
	++a[x][val[x]];
	for(int i=h[x];i;i=c[i].x)
	  if(c[i].y!=fa)
	  {
	  	dfs(c[i].y,x),FWT_XOR(a[x]),FWT_XOR(a[c[i].y]);
	  	for(int j=0;j<=lim;++j)
	  	  a[x][j]=1ll*a[x][j]*a[c[i].y][j]%mod;
	  	FWT_XOR(a[x],-1),FWT_XOR(a[c[i].y],-1);
	  }
	++a[x][0];
}
int main()
{
	T=read();
	while(T--)
	{
		n=read(),lim=read(),num=0;
		memset(h,0,sizeof(h));
		memset(a,0,sizeof(a));
		for(int i=1;i<=n;++i)
		  val[i]=read();
		for(int i=1;i<n;++i)
		  add(read(),read());
		dfs();
		for(int i=0,x;i<lim;++i)
		  {
		  	x=0;
		  	for(int j=1;j<=n;++j)
		  	  {
		  	  	x+=a[j][i]-(i==0);
		  	  	if(x>=mod) x-=mod;
			  }
			printf("%d",x),i!=lim-1?printf(" "):0;
		  }
		printf("\n");
	}
	return 0;
}
```

