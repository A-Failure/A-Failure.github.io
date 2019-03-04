---

layout:     post
title:      "[LUOGU4146]序列终结者(Fhq_Treap)"
date:       2018-03-21
author:     "Dispwnl"
header-img: "img/used/005.jpg"
catalog: true
tags:
    - 平衡树
---
>谨以此纪念一个浪费的下午

今天本来是想看看数论的...

可为什么全用来搞平衡树了喂！T_T

## [题目](https://www.luogu.org/problemnew/show/P4146)
### 题目背景
网上有许多题，就是给定一个序列，要你支持几种操作：A、B、C、D。一看另一道题，又是一个序列

要支持几种操作：D、C、B、A。尤其是我们这里的某人，出模拟试题，居然还出了一道这样的，真是没技术含量……这样

我也出一道题，我出这一道的目的是为了让大家以后做这种题目有一个“库”可以依靠，没有什么其他的意思。这道题目

就叫序列终结者吧。

### 题目描述
给定一个长度为N的序列，每个序列的元素是一个整数（废话）。要支持以下三种操作：

将$[L,R]$这个区间内的所有数加上$V$。

将$[L,R]$这个区间翻转，比如1 2 3 4变成4 3 2 1。

求$[L,R]$这个区间中的最大值。最开始所有元素都是0。

### 输入输出格式
#### 输入格式：
第一行两个整数N，M。M为操作个数。

以下$M$行，每行最多四个整数，依次为K，L，R，V。K表示是第几种操作，如果不是第1种操作则K后面只有两个数。

#### 输出格式：
对于每个第3种操作，给出正确的回答。

### 输入输出样例
#### 输入样例#1：
```plain
4 4
1 1 3 2
1 2 4 -1
2 1 3
3 2 4
```
#### 输出样例#1：

```plain
2
```
### 说明
$N\leq 50000,M\leq 100000$。

### 题解

序列终结者这题我本来是用$splay$搞的~~然后知道$Fhq$ $Treap$可以搞区间就与$splay$说拜拜了~~

然后今天想用ta练练$Fhq$ $Treap$

操作都挺简单，建树直接暴力建~~高级建法我不会啊~~，然后搞一大堆$merge$ $split$，处理两个标记

然后就开心的打完了

然后就WA了~~不这不可能这是评测姬的锅~~。。。

尴尬的检查了一下午竟然没找出错。。。

无意瞄了一眼输出函数

woccccc $split$为什么从$l$开始分啊~~我明明是粘的板子~~

改过了就<code>A</code>了T_T~~竟然跑的比$splay$慢~~

### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<cstdlib>
using namespace std;
const int MAX=5e4+1;
int tot,rt;
int max(int x,int y)
{
    return x>y?x:y;
}
int read()
{
    int x=0,f=1;
    char ch=getchar();
    for(;!isdigit(ch);f=(ch=='-')?-1:1,ch=getchar());
    for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
    return x*f;
}
struct Fhq_Treap{
    int w[MAX],siz[MAX],lazy[MAX],maxn[MAX],pos[MAX];
    int son[MAX][2];
    bool fl[MAX];
    void pus(int x)
    {
        siz[x]=siz[son[x][1]]+siz[son[x][0]]+1;
        if(son[x][1]&&son[x][0]) maxn[x]=max(w[x],max(maxn[son[x][1]],maxn[son[x][0]]));
        else if(son[x][1]) maxn[x]=max(w[x],maxn[son[x][1]]);
        else if(son[x][0]) maxn[x]=max(w[x],maxn[son[x][0]]);
        else maxn[x]=w[x];
    }
    void rever(int x)
    {
        swap(son[x][0],son[x][1]);
        fl[x]^=1;
    }
    void add(int x,int v)
    {
        lazy[x]+=v,maxn[x]+=v,w[x]+=v;
    }
    void down(int x)
    {
        if(fl[x]&&x)
        {
            if(son[x][1]) rever(son[x][1]);
            if(son[x][0]) rever(son[x][0]);
            fl[x]=0;
        }
        if(lazy[x]&&x)
        {
            if(son[x][1]) add(son[x][1],lazy[x]);
            if(son[x][0]) add(son[x][0],lazy[x]);
            lazy[x]=0;
        }
    }
    int merge(int x,int y)
    {
        if(x) down(x);
        if(y) down(y);
        if(!x||!y) return x+y;
        if(pos[x]<pos[y])
        {
            son[x][1]=merge(son[x][1],y);
            pus(x);
            return x;
        }
        else
        {
            son[y][0]=merge(x,son[y][0]);
            pus(y);
            return y;
        }
    }
    void split(int i,int k,int &a,int &b)
    {
        if(!i) a=b=0;
        else
        {
            down(i);
            if(k<=siz[son[i][0]])
            b=i,split(son[i][0],k,a,son[i][0]);
            else
            a=i,split(son[i][1],k-siz[son[i][0]]-1,son[i][1],b);
            pus(i);
        }
    }
    void ins(int l)
    {
        int a,b;
        split(rt,l,a,b);
        pos[++tot]=rand();
        rt=merge(merge(a,tot),b);
    }
    void ADD(int l,int r,int x)
    {
        int L=r-l+1;
        int a,b,c,d;
        split(rt,l-1,a,b);
        split(b,L,c,d);
        add(c,x);
        rt=merge(a,merge(c,d));
    }
    void REVER(int l,int r)
    {
        int L=r-l+1;
        int a,b,c,d;
        split(rt,l-1,a,b);
        split(b,L,c,d);
        rever(c);
        rt=merge(a,merge(c,d));
    }
    void GET_MAX(int l,int r)
    {
        int L=r-l+1;
        int a,b,c,d;
        split(rt,l-1,a,b);
        split(b,L,c,d);
        printf("%d\n",maxn[c]);
        rt=merge(a,merge(c,d));
    }
}Tree;
int n,m;
int main()
{
    n=read(),m=read();
    for(int i=1;i<=n;++i)
      Tree.ins(i);
    for(int i=1;i<=m;++i)
      {
      	int op=read(),l=read(),r=read();
      	if(op==1)
      	Tree.ADD(l,r,read());
      	else if(op==2)
      	Tree.REVER(l,r);
      	else if(op==3)
      	Tree.GET_MAX(l,r);
      }
    return 0;
}
```
