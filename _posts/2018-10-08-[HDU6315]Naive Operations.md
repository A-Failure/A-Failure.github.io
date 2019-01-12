---
layout:     post
title:      "[HDU6315]Naive Operations"
date:       2018-10-08
author:     "Dispwnl"
header-img: "img/used/0896.jpg"
catalog: true
tags:
    - 线段树
---
## [题目](https://vjudge.net/problem/HDU-6315)
### 题目大意
给定一个长度为$n$的排列$b$，需要维护一个长度同样为$n$的序列$a$

有两种操作：

- 给定区间$[l,r]$，$a_{l...r}$全部$+1$
- 给定区间$[l,r]$，求$\sum_{i=l}^{r}\left\lfloor\frac{a_i}{b_i}\right\rfloor$

### Sample Input
```plain
5 12
1 5 2 4 3
add 1 4
query 1 4
add 2 5
query 2 5
add 3 5
query 1 5
add 2 4
query 1 4
add 2 5
query 2 5
add 2 2
query 1 5
```
### Sample Output
```plain
1
1
2
4
4
6
```
### 题解
可以想到当$a_i$加的值等于$b_i$的倍数的时候，$\left\lfloor\frac{a_i}{b_i}\right\rfloor$才会发生变化

所以可以维护区间最大值，当某个点达到对应的$b_i$的时候重新赋值为$0$，同时这个点的答案$+1$

发现好像不太好维护？

反过来就好了，起初所有值为$b_i$，每次区间$-1$，当区间最小值为$0$的时候找到对应的点进行重新赋值

因为是排列，所以每次修改最多修改一个答案，复杂度就保证是$O(nlogn)$的

### 代码
```
# include<bits/stdc++.h>
# define tl (k<<1)
# define tr (k<<1|1)
# define mid (l+r>>1)
using namespace std;
const int MAX=4e5+5;
struct p{
	int x,b,ans,lazy;
}s[MAX];
int n,q;
char a[11];
int read()
{
	int x(0);
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
void pus(int k)
{
	s[k].x=min(s[tl].x,s[tr].x),s[k].ans=s[tl].ans+s[tr].ans;
}
void build(int l,int r,int k)
{
	s[k].ans=s[k].lazy=0;
	if(l==r) return void(s[k].x=s[k].b=read());
	build(l,mid,tl),build(mid+1,r,tr),pus(k);
}
void down(int k)
{
	if(!s[k].lazy) return;
	int f=s[k].lazy;
	s[k].lazy=0,s[tl].lazy+=f,s[tr].lazy+=f,s[tl].x+=f,s[tr].x+=f;
}
void change(int l,int r,int k,int L,int R)
{
	if(l==L&&r==R)
	{
		--s[k].x;
		if(s[k].x) return void(--s[k].lazy);
		else if(l==r)
		{
			s[k].x=s[k].b;
			return void(++s[k].ans);
		}
	}
	down(k);
	if(R<=mid) change(l,mid,tl,L,R);
	else if(L>mid) change(mid+1,r,tr,L,R);
	else change(l,mid,tl,L,mid),change(mid+1,r,tr,mid+1,R);
	pus(k);
}
int ask(int l,int r,int k,int L,int R)
{
	if(l==L&&r==R) return s[k].ans;
	down(k);
	if(R<=mid) return ask(l,mid,tl,L,R);
	if(L>mid) return ask(mid+1,r,tr,L,R);
	return ask(l,mid,tl,L,mid)+ask(mid+1,r,tr,mid+1,R);
}
int main()
{
	while(scanf("%d%d",&n,&q)==2)
	{
		build(1,n,1);
		for(int i=1,l,r;i<=q;++i)
		  {
	 	 	scanf("%s",a),l=read(),r=read();
		  	if(a[0]=='a') change(1,n,1,l,r);
		  	else if(a[0]=='q') printf("%d\n",ask(1,n,1,l,r));
		  }
	}
	return 0;
}
```
