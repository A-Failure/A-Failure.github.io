---
layout:     post
title:      "[HDU5306]Gorgeous Sequence"
date:       2018-10-15
author:     "Dispwnl"
header-img: "img/used/7654.jpg"
catalog: true
tags:
    - 线段树
---
## [题目](https://vjudge.net/problem/HDU-5306)
### 题目大意
给定一个序列，有三种操作：
- 区间取$min$
- 区间求和
- 区间查询最大值

多组数据

### Sample Input
```plain
1
5 5
1 2 3 4 5
1 1 5
2 1 5
0 3 5 3
1 1 5
2 1 5
```
### Sample Output
```plain
5
15
3
12
```
### 题解
#### 我真傻，真的，我以后再也不用宏定义<code>max</code>了

听说是$jry$线段树的写法？

难点是区间取$min$

线段树每个节点存最大值、严格次大值、最大值个数和区间和

- 如果取$min$的值$x$不小于区间最大值，说明ta没有更新可能了，直接<code>return</code>
- 如果$x$小于区间最大值大于区间严格次大值，打上标记，$sum_k-=(x-maxn_k)\times num_k$
- 如果次大值不小于$x$？暴力递归修改，随机数据均摊复杂度是$O(logn)$

然后因为觉得可能要卡卡常，加上了```# define  max (x>y?x:y)```……

后面```max(ask(),ask())```

<code>TLE</code>
<code>TTLE</code>
<code>TTTLE</code>
<code>TTTTLE</code>
<code>TTTTTLE</code>
<code>TTTTTTLE</code>
<code>TTTTTLE</code>
<code>TTTTLE</code>
<code>TTTLE</code>
<code>TTTLE</code>
<code>TTLE</code>
<code>TLE</code>

~~你经历过拿三份题解对拍的绝望吗~~

自闭了告辞![](/img/qaq/346.jpg)

### 代码
```c++
# include<bits/stdc++.h>
# define tl (k<<1)
# define tr (k<<1|1)
# define mid (l+r>>1)
# define LL long long
using namespace std;
const int MAX=4e6+5;
struct p{
	int maxn,_maxn,num;
	LL sum;
}s[MAX];
int T,n,m,cnt;
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
	s[k]._maxn=max(s[tl]._maxn,s[tr]._maxn),s[k].sum=s[tl].sum+s[tr].sum;
	if(s[tl].maxn>s[tr].maxn) s[k].maxn=s[tl].maxn,s[k].num=s[tl].num,s[k]._maxn=max(s[k]._maxn,s[tr].maxn);
	if(s[tl].maxn<s[tr].maxn) s[k].maxn=s[tr].maxn,s[k].num=s[tr].num,s[k]._maxn=max(s[k]._maxn,s[tl].maxn);
	if(s[tl].maxn==s[tr].maxn) s[k].maxn=s[tl].maxn,s[k].num=s[tl].num+s[tr].num;
}
void build(int l=1,int r=n,int k=1)
{
	s[k]._maxn=-1;
	if(l==r)
	{
		s[k].maxn=s[k].sum=read();
		return void(s[k].num=1);
	}
	build(l,mid,tl),build(mid+1,r,tr),pus(k);
}
void down(int k)
{
	int dis=s[k].maxn;
	if(s[tl].maxn>dis) s[tl].sum-=1ll*(s[tl].maxn-dis)*s[tl].num,s[tl].maxn=dis;
	if(s[tr].maxn>dis) s[tr].sum-=1ll*(s[tr].maxn-dis)*s[tr].num,s[tr].maxn=dis;
}
int ask_1(int l,int r,int k,int L,int R)
{
	if(l==L&&r==R) return s[k].maxn;
	down(k);
	if(R<=mid) return ask_1(l,mid,tl,L,R);
	else if(L>mid) return ask_1(mid+1,r,tr,L,R);
	else return max(ask_1(l,mid,tl,L,mid),ask_1(mid+1,r,tr,mid+1,R));
}
LL ask_2(int l,int r,int k,int L,int R)
{
	if(l==L&&r==R) return s[k].sum;
	down(k);
	if(R<=mid) return ask_2(l,mid,tl,L,R);
	else if(L>mid) return ask_2(mid+1,r,tr,L,R);
	else return ask_2(l,mid,tl,L,mid)+ask_2(mid+1,r,tr,mid+1,R);
}
void change(int l,int r,int k,int L,int R,int dis)
{
	if(s[k].maxn<=dis) return;
	if(l==L&&r==R)
	{
		if(s[k]._maxn<dis)
		{
			s[k].sum-=1ll*(s[k].maxn-dis)*s[k].num;
			return void(s[k].maxn=dis);
		}
	}
	down(k);
	if(R<=mid) change(l,mid,tl,L,R,dis);
	else if(L>mid) change(mid+1,r,tr,L,R,dis);
	else change(l,mid,tl,L,mid,dis),change(mid+1,r,tr,mid+1,R,dis);
	pus(k);
}
int main()
{
	T=read();
	while(T--)
	{
		n=read(),m=read(),build();
		for(int i=1,op,x,y;i<=m;++i)
		  {
		  	op=read(),x=read(),y=read();
		  	if(!op) change(1,n,1,x,y,read());
		  	else if(op==1) printf("%d\n",ask_1(1,n,1,x,y));
		  	else if(op==2) printf("%lld\n",ask_2(1,n,1,x,y));
		  }
	}
}
```
