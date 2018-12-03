---
layout:         post
title:          "[LUOGU2418]yyy loves OI IV"
date:           2018-12-03
auther:         "Dispwnl"
header-img:     "img/used/45600.jpg"
catalog:    true
tags:
    - 动态规划
    - 线段树
---
## [题目](https://www.luogu.org/problemnew/show/P2418)
### 题目背景
某校2015届有两位OI神牛，yyy和c01。

### 题目描述
全校除他们以外的N名学生，每人都会膜拜他们中的某一个人。现在老师要给他们分宿舍了。但是，问题来了：

同一间宿舍里的人要么膜拜同一位大牛，要么膜拜yyy和c01的人数的差的绝对值不超过M。否则他们就会打起来。

为了方便，老师让N名学生站成一排，只有连续地站在一起的人才能分进同一个宿舍。

假设每间宿舍能容纳任意多的人，请问最少要安排几个宿舍？

### 输入输出格式
#### 输入格式：
第一行，两个正整数N和M

第2……N+1行，每行一个整数1或2，第i行的数字表示从左往右数第i-1个人膜拜的大牛。

1表示yyy，2表示c01.

#### 输出格式：
一行，一个整数，表示最少要安排几个宿舍。

### 输入输出样例
#### 输入样例#1： 
```
5 1
1
1
2
2
1
```
#### 输出样例#1： 
```
1
```
### 说明
难度题，做好心理准备~

|测试点编号 |$N$的范围 |$M$的范围|
|:-----------:|:-----------:|:-----------:|
|1~3 |$\le2,500$| $\le 10$|
|4~5 |$\le 500,000$| $\le 10$|
|6~10|$\le 500,000$| $\le 2,000$|

### 题解
首先$O(n^2)$的$dp$方程很好搞出来，求一个前缀和$sum$，则

$f_i=min(f_{j-1})+1,j<i,\vert sum_i-sum_{j-1}\vert \le m$

或是$f_i=min(f_{j-1})+1,j<i,\vert sum_i-sum_{j-1}\vert=i-j+1$

后面的式子可以$O(n)$扫一遍搞出来

对于前面的不等式$\vert sum_i-sum_{j-1}\vert \le m$，可以拆开讨论：

若$sum_i\le sum_{j-1}$，则$sum_i-m\le sum_{j-1}\le sum_i$

若$sum_i\ge sum_{j-1}$，则$sum_i+m\ge sum_{j-1}\ge sum_i$

显然是两个区间，用线段树维护即可

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
const int MAX=1e6+1e4+5,N=5e5+5,MX=5e5+3e3+1;
struct p{
    int x;
    p(){x=1e9;}
}s[MAX<<2];
int n,m;
int sum[N],a[N],f[N];
int read()
{
    int x(0);
    char ch=getchar();
    for(;!isdigit(ch);ch=getchar());
    for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
    return x;
}
void change(int l,int r,int k,int x,int dis)
{
    s[k].x=min(s[k].x,dis);
    if(l==r) return;
    if(x<=mid) change(l,mid,tl,x,dis);
    else change(mid+1,r,tr,x,dis);
}
int ask(int l,int r,int k,int L,int R)
{
    if(l==L&&r==R) return s[k].x;
    if(R<=mid) return ask(l,mid,tl,L,R);
    if(L>mid) return ask(mid+1,r,tr,L,R);
    return min(ask(l,mid,tl,L,mid),ask(mid+1,r,tr,mid+1,R));
}
int main()
{
    n=read(),m=read();
    for(int i=1;i<=n;++i)
      {
      	a[i]=read();
      	if(a[i]==1) sum[i]=sum[i-1]+1;
      	else sum[i]=sum[i-1]-1;
      }
    memset(f,1,sizeof(f));
    f[1]=1,change(0,MAX-1,1,sum[1]+MX,1),change(0,MAX-1,1,MX,0);
    int qwq=a[1]==1,qaq=a[1]==2,qvq=(a[1]==1?0:1e9),qcq=(a[1]==2?0:1e9);
    for(int i=2,A1,A2,A3;i<=n;++i)
      {
      	if(a[i]==1) A3=qvq,++qwq,qaq=0,qcq=1e9;
      	if(a[i]==2) A3=qcq,++qaq,qwq=0,qvq=1e9;
      	A1=ask(0,MAX-1,1,sum[i]+MX-m,sum[i]+MX),A2=ask(0,MAX-1,1,sum[i]+MX,sum[i]+MX+m);
      	f[i]=min(A3,min(A1,A2))+1,change(0,MAX-1,1,sum[i]+MX,f[i]);
      	if(a[i]==1) qvq=min(qvq,f[i-1]);
      	if(a[i]==2) qcq=min(qcq,f[i-1]);
      }
    return printf("%d",f[n]),0;
}
```
