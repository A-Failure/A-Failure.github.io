---
layout:     post
title:      "[CF1039B]Subway Pursuit"
date:       2018-09-06
author:     "Dispwnl"
header-img: "img/used/1645.jpg"
catalog: true
tags:
    - 概率与期望
    - 瞎搞
---
## [题目](http://codeforces.com/problemset/problem/1039/B)
### 题目大意
交互题

在$1$到$n$里有一个运动的点，要求找到这个点，每次可以查询一个区间内有没有这个点，每次这个点往左或者往右移动$1$到$k$个位置，要求要在$4500$次查询内找到这个点的位置

### Example
#### input
```plain
10 2
Yes
No
Yes
Yes
```
#### output
```plain
3 5
3 3
3 4
5 5
```
### 题解
##### 震惊！一交互题竟能用$rand$轻松水过！$99\%$的网友不知道这件事
首先这道题可以用二分缩小范围，把范围缩小到一个合适的~~看心情~~范围的范围内

然后就可以$rand$一个这个区间内的数，判断是不是，不是的话肯定会增加范围，如果超过这个范围就继续二分缩小，重复这个过程

为什么这样能找到正确答案呢？假设$rand$了$t$次，合适区间长度为$l$，那么找到正确答案的概率大约是$1-(\frac{l-1}{l})^t$

$t$最大能达到$4000+$这样子，这个概率什么概念呢？就是给你几十个球，让你抓$4000$多次来找到一个指定的球，基本上就是十拿九稳的事了

然而一个真正的非酋是不会相信概率这些东西的![](/img/study/feiqiu.png)

### 代码
```
# include<iostream>
# include<cstring>
# include<cstdlib>
# include<cstdio>
# include<ctime>
# include<algorithm>
# define LL long long
using namespace std;
LL n,L,R;
int k;
char a[10];
bool query(LL l,LL r)
{
	cout<<l<<" "<<r<<endl;
	fflush(stdout);
	scanf("%s",a);
	if(a[0]=='Y') return 1;
	return 0;
}
int main()
{
	srand(time(0));
	cin>>n>>k,L=1,R=n;
	while(1)
	{
		while(R-L>50)
		{
			LL mid(L+R>>1);
			if(query(L,mid)) R=mid;
			else L=mid+1;
			L=max(1ll,L-k),R=min(n,R+k);
		}
		LL p=rand()%(R-L+1)+L;
		if(query(p,p)) return 0;
		L=max(1ll,L-k),R=min(n,R+k);
	}
	return 0;
}
```
