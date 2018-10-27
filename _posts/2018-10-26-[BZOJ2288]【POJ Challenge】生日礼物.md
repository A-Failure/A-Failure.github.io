---
layout:         post
title:          "[BZOJ2288]【POJ Challenge】生日礼物"
date:           2018-10-26
author:         "Dispwnl"
header-img:     
catalog:    true
tags:
    - 线段树
    - 瞎搞
---
## [题目](https://www.lydsy.com/JudgeOnline/problem.php?id=2288)
### Description
ftiasch 18岁生日的时候，lqp18_31给她看了一个神奇的序列 A1, A2, ..., AN. 她被允许选择不超过 M 个连续的部分作为自己的生日礼物。

自然地，ftiasch想要知道选择元素之和的最大值。你能帮助她吗？

### Input
第1行，两个整数 N (1 ≤ N ≤ 10^5) 和 M (0 ≤ M ≤ 10^5), 序列的长度和可以选择的部分。

第2行， N 个整数 A1, A2, ..., AN (0 ≤ |Ai| ≤ 10^4), 序列。

### Output
 
一个整数，最大的和。

### Sample Input
```
5 2 
2 -3 2 -1 2
```
### Sample Output
```
5
```
### 题解
考虑$m=1$的时候

线段树维护最大子段和！

当$m>1$的时候，显然是不能多次求最大子段和（有重复的）

考虑将选出的最大子段翻转（正负变化）？

- 当子段值为正，翻转后第二次求的答案肯定不包含这个区间（贪心思想）
- 当子段值为负，后面的操作完全可以忽略，因为这时最优策略就是不选

所以用线段树简单维护一下就可以了，维护$18$个值：区间最大值，区间最小值，区间从左开始最大连续值、延伸到的位置，区间从左开始最小连续值、延伸到的位置，区间从右开始最大连续值、延伸到的位置，区间从右开始最小连续值、延伸到的位置，区间和，区间左、右端点，区间最大子段左、右端点，区间最小子段左、右端点，翻转标记qwq
![](/img/avatar-hux-home.jpg)

### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
# define tl (k<<1)
# define tr (k<<1|1)
# define mid (l+r>>1)
using namespace std;
const int MAX=4e5+5;
struct p{
    int l,r,maxn_,minn_,l_max,r_max,l_min,r_min,sum,l_mx,r_mx,l1_mx,r1_mx,l_mn,r_mn,l1_mn,r1_mn,tag;
}s[MAX];
int n,m,ans;
int read()
{
    int x=0,fl=1;
    char ch=getchar();
    for(;!isdigit(ch);fl=(ch=='-')?-1:1,ch=getchar());
    for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
    return x*fl;
}
void pus(p &a,p b,p c)
{
    a.sum=b.sum+c.sum;
    if(b.l_max>b.sum+c.l_max) a.l_max=b.l_max,a.l1_mx=b.l1_mx;
    else a.l_max=b.sum+c.l_max,a.l1_mx=c.l1_mx;
    if(b.l_min<b.sum+c.l_min) a.l_min=b.l_min,a.l1_mn=b.l1_mn;
    else a.l_min=b.sum+c.l_min,a.l1_mn=c.l1_mn;
    if(c.r_max>c.sum+b.r_max) a.r_max=c.r_max,a.r1_mx=c.r1_mx;
    else a.r_max=c.sum+b.r_max,a.r1_mx=b.r1_mx;
    if(c.r_min<c.sum+b.r_min) a.r_min=c.r_min,a.r1_mn=c.r1_mn;
    else a.r_min=c.sum+b.r_min,a.r1_mn=b.r1_mn;
    a.maxn_=-1e9;
    if(a.maxn_<a.l_max) a.maxn_=a.l_max,a.l_mx=a.l,a.r_mx=a.l1_mx;
    if(a.maxn_<a.r_max) a.maxn_=a.r_max,a.l_mx=a.r1_mx,a.r_mx=a.r;
    if(a.maxn_<b.maxn_) a.maxn_=b.maxn_,a.l_mx=b.l_mx,a.r_mx=b.r_mx;
    if(a.maxn_<c.maxn_) a.maxn_=c.maxn_,a.l_mx=c.l_mx,a.r_mx=c.r_mx;
    if(a.maxn_<b.r_max+c.l_max) a.maxn_=b.r_max+c.l_max,a.l_mx=b.r1_mx,a.r_mx=c.l1_mx;
    a.minn_=1e9;
    if(a.minn_>a.l_min) a.minn_=a.l_min,a.l_mn=a.l,a.r_mn=a.l1_mn;
    if(a.minn_>a.r_min) a.minn_=a.r_min,a.l_mn=a.r1_mn,a.r_mn=a.r;
    if(a.minn_>b.minn_) a.minn_=b.minn_,a.l_mn=b.l_mn,a.r_mn=b.r_mn;
    if(a.minn_>c.minn_) a.minn_=c.minn_,a.l_mn=c.l_mn,a.r_mn=c.r_mn;
    if(a.minn_>b.r_min+c.l_min) a.minn_=b.r_min+c.l_min,a.l_mn=b.r1_mn,a.r_mn=c.l1_mn;
}
void build(int l=1,int r=n,int k=1)
{
    s[k].l=s[k].l1_mx=s[k].l1_mn=s[k].l_mn=s[k].l_mx=l,s[k].r=s[k].r1_mx=s[k].r1_mn=s[k].r_mn=s[k].r_mx=r;
    if(l==r) return void(s[k].maxn_=s[k].minn_=s[k].l_max=s[k].r_max=s[k].l_min=s[k].r_min=s[k].sum=read());
    build(l,mid,tl),build(mid+1,r,tr),pus(s[k],s[tl],s[tr]);
}
void work(int k)
{
    s[k].sum=-s[k].sum,s[k].tag^=1;
    swap(s[k].l_max,s[k].l_min),swap(s[k].r_max,s[k].r_min),swap(s[k].maxn_,s[k].minn_),swap(s[k].l_mx,s[k].l_mn),swap(s[k].r_mx,s[k].r_mn),swap(s[k].r1_mx,s[k].r1_mn),swap(s[k].l1_mx,s[k].l1_mn);
    s[k].minn_=-s[k].minn_,s[k].maxn_=-s[k].maxn_,s[k].l_max=-s[k].l_max,s[k].l_min=-s[k].l_min,s[k].r_max=-s[k].r_max,s[k].r_min=-s[k].r_min;
}
void down(int k)
{
    if(!s[k].tag) return;
    s[k].tag=0,work(tl),work(tr);
}
void change(int l,int r,int k,int L,int R)
{
    if(l==L&&r==R) return void(work(k));
    down(k);
    if(R<=mid) change(l,mid,tl,L,R);
    else if(L>mid) change(mid+1,r,tr,L,R);
    else change(l,mid,tl,L,mid),change(mid+1,r,tr,mid+1,R);
    pus(s[k],s[tl],s[tr]);
}
int main()
{
    n=read(),m=read(),build();
    for(int i=1;i<=m;++i)
      {
        ans+=max(s[1].maxn_,0);
        if(s[1].maxn_>=0) change(1,n,1,s[1].l_mx,s[1].r_mx);
      }
    return printf("%d",ans),0;
}
```
