---
layout:     post
title:      "[CF662C]Binary Table"
date:       2018-08-23
author:     "Dispwnl"
header-img: "img/used/546.jpg"
catalog: true
tags:
    - FWT
---
## [题目](http://codeforces.com/problemset/problem/662/C)
### 题目大意
有一个 n 行 m 列的表格，每个元素都是 0/1 ，每次操作可以选择一行或一列，把 0/1 翻转，即把 0 换为 1 ，把 1 换为 0 。请问经过若干次操作后，表格中最少有多少个 1 。

### Example
#### input
```
3 4
0110
1010
0111
```
#### output
```
2
```
### 题解

发现$n$非常小，考虑状态压缩

用一个小于$2^n$的数来表示行是否翻转，0表示不翻转，1表示翻转

对于一列$j$（也用一个小于$2^n$的数表示），ta经过$i$状态的行翻转之后的状态为$i$ xor $j$（对应部分为1就翻转，0就不翻转）

用$a_i$表示列状态为$i$的总列数，$b_i$表示列状态为$i$这一列最少的1的个数（就是$min(num,n-num)）$，$ans_i$表示行翻转状态为$i$的时候矩阵最少的1的个数

因为有<a href="http://www.codecogs.com/eqnedit.php?latex=\\\because&space;i&space;\oplus&space;j=k&space;\\\therefore&space;j&space;\oplus&space;k=i" target="_blank"><img src="http://latex.codecogs.com/gif.latex?\\\because&space;i&space;\oplus&space;j=k&space;\\\therefore&space;j&space;\oplus&space;k=i" title="\\\because i \oplus j=k \\\therefore j \oplus k=i" /></a>

所以$ans_i=\sum_{j \oplus k=i}a_j*b_k$

用$FWT$优化一下就好了，复杂度$O(2^n*n)$

### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
# define LL long long
using namespace std;
const int MAX=(1<<20)+1;
int n,m,len,ans=1e9;
LL a[MAX],b[MAX];
string A[21];
void FWT_XOR(LL *A,int op)
{
	for(int i=1;i<len;i<<=1)
	  for(int p=i<<1,j=0;j<len;j+=p)
	    for(int k=0;k<i;++k)
	      {
	      	LL x=A[j+k],y=A[i+j+k];
	      	A[j+k]=x+y,A[i+j+k]=x-y;
	      	if(op==-1) A[j+k]>>=1,A[i+j+k]>>=1;
		  }
}
int main()
{
	scanf("%d%d",&n,&m),len=1<<n;
	for(int i=0;i<n;++i)
	  cin>>A[i];
	for(int x=0,i=0;i<m;++i,++a[x],x=0)
	  for(int j=0;j<n;x+=(A[j++][i]-'0')*(1<<(j-1)));
	for(int i=0;i<len;++i)
	  {
	  	int x=i;
	  	while(x) b[i]+=x%2,x/=2;
	  	b[i]=min(b[i],n-b[i]);
	  }
	FWT_XOR(a,1),FWT_XOR(b,1);
	for(int i=0;i<len;++i)
	  a[i]*=b[i];
	FWT_XOR(a,-1);
	for(int i=0;i<len;++i)
	  ans=min(1ll*ans,a[i]);
	return printf("%d",ans),0;
}
```
