---
layout:     post
title:      "[USACO10FEB]慢下来Slowing down"
date:       2017-12-24
author:     "Dispwnl"
header-img: "img/used/780.jpg"
catalog: true
tags:
    - 线段树
    - 树链剖分
    - 瞎搞
---
## [题目](https://www.luogu.org/problemnew/show/P2982)
### 题目大意
每天Farmer John的N头奶牛(1 <= N <= 100000，编号1…N)从粮仓走向他的自己的牧场。牧场构成了一棵树，粮仓在1号牧场。恰好有N-1条道路直接连接着牧场，使得牧场之间都恰好有一条路径相连。第i条路连接着A_i，B_i，(1 <= A_i <= N; 1 <= B_i <= N)。 奶牛们每人有一个私人牧场P_i (1 <= P_i <= N)。粮仓的门每次只能让一只奶牛离开。耐心的奶牛们会等到他们的前面的朋友们到达了自己的私人牧场后才离开。首先奶牛1离开，前往P_1；然后是奶牛2，以此类推。

当奶牛i走向牧场P_i时候，他可能会经过正在吃草的同伴旁。当路过已经有奶牛的牧场时，奶牛i会放慢自己的速度，防止打扰他的朋友。

FJ想要知道奶牛们总共要放慢多少次速度。

### 输入输出样例
#### 输入样例#1： 
```
5 
1 4 
5 4 
1 3 
2 4 
4 
2 
1 
5 
3 
```
#### 输出样例#1： 
```
0 
1 
0 
2 
1 
```
### 题解

用$dfs$序储存每个点到$1$号点的距离和路线，因为没有值所以只用储存懒标记

懒标记表示$1$号点到自己农场这段区间有多少回到农场的牛，当一头牛走时目标点$+1$，并顺便下推懒标记

 ![](/img/study/slowdown.png) 

如图所示（样例），当去$2​$的牛走时，必定经过$4​$号点，$4​$号点已经有牛了，所以下推标记，$5​$号点、$2​$号点懒标记各$+1​$

最后统计每个点懒标记

### 代码
```c++
# include<iostream>
# include<cstdio>
# include<cstring>
using namespace std;
struct p{
    int x,y;
}c[200001];
struct q{
    int dfn,l;
}d[100001];
int n,num,tot;
int lazy[400001],h[100001];
void add(int x,int y)
{
    c[++num].x=h[x];
    c[num].y=y;
    h[x]=num;
}
void dfs(int x)
{
    d[x].dfn=++tot;
    for(int i=h[x];i;i=c[i].x)
      if(!d[c[i].y].dfn)
      dfs(c[i].y),d[x].l+=d[c[i].y].l;
}
void down(int k)
{
    lazy[k<<1]+=lazy[k];
    lazy[k<<1|1]+=lazy[k];
    lazy[k]=0;
}
void change(int x,int y,int k,int l,int r)
{
    if(l>=x&&r<=y)
    {
        lazy[k]++;
        return;
    }
    if(lazy[k]) down(k);
    int mid=(l+r)>>1;
    if(x<=mid) change(x,y,k<<1,l,mid);
    if(y>mid) change(x,y,k<<1|1,mid+1,r); 
}
int ask(int x,int k,int l,int r)
{
    if(l==r)
    return lazy[k];
    if(lazy[k]) down(k);
    int mid=(l+r)>>1;
    if(x<=mid) return ask(x,k<<1,l,mid);
    else return ask(x,k<<1|1,mid+1,r);
}
int main()
{
    cin>>n;
    for(int i=1;i<=n;i++)
      d[i].l=1;
    for(int i=1;i<n;i++)
      {
          int x,y;
          cin>>x>>y;
          add(x,y);
          add(y,x);
      }
    dfs(1);
    for(int i=1;i<=n;i++)
      {
          int x;
          cin>>x;
          cout<<ask(d[x].dfn,1,1,n)<<endl;
          change(d[x].dfn,d[x].dfn+d[x].l-1,1,1,n);
      }
    return 0;
}
```
也可以用树链剖分做

处理出每一条链后，每次回自己农场时，剖分统计区间和，然后自己农场值$+1$

跑的还不慢，$O_2$后$200ms$左右

### 代码
```c++
# include<iostream>
# include<cstdio>
# include<cstring>
#define mid ((l+r)>>1)
#define tl (k<<1)
#define tr (k<<1|1)
#define ini inline int
#define inv inline void
#define is isdigit(ch)
using namespace std;
const int MAX=1e5+1;
struct p{
    int x;
}s[MAX<<2];
struct q{
    int x,y;
}c[MAX<<1];
int n,num,cnt;
int h[MAX],id[MAX],top[MAX],son[MAX],fa[MAX],siz[MAX],d[MAX];
ini read()
{
    int x=0;
    char ch=getchar();
    while(!is) ch=getchar();
    while(is)
    {
        x=x*10+ch-48;
        ch=getchar();
    }
    return x;
}
inv add(int x,int y)
{
    c[++num].x=h[x];
    c[num].y=y;
    h[x]=num;
}
void dfs(int x,int f)
{
    d[x]=d[f]+1;
    fa[x]=f;
    siz[x]=1;
    for(int i=h[x];i;i=c[i].x)
      {
          int y=c[i].y;
          if(y==f) continue;
          dfs(y,x);
          siz[x]+=siz[y];
          if(siz[son[x]]<siz[y])
          son[x]=y;
      }
}
void dfs1(int x,int tp)
{
    top[x]=tp;
    id[x]=++cnt;
    if(son[x]) dfs1(son[x],tp);
    for(int i=h[x];i;i=c[i].x)
      {
          int y=c[i].y;
          if(y==fa[x]||y==son[x]) continue;
          dfs1(y,y);
      }
}
inv pus(int k)
{
    s[k].x=s[tl].x+s[tr].x;
}
void change(int l,int r,int k,int x)
{
    if(l==r)
    {
        s[k].x++;
        return;
    }
    if(x<=mid) change(l,mid,tl,x);
    else change(mid+1,r,tr,x);
    pus(k);
}
int ask(int l,int r,int k,int L,int R)
{
    if(l>=L&&r<=R)
    return s[k].x;
    if(l>R||r<L) return 0;
    return ask(l,mid,tl,L,R)+ask(mid+1,r,tr,L,R);
}
int ASK(int x,int y)
{
    int ans=0;
    while(top[x]!=top[y])
    {
        if(d[top[x]]<d[top[y]]) swap(x,y);
        ans+=ask(1,n,1,id[top[x]],id[x]);
        x=fa[top[x]];
    }
    if(d[x]>d[y]) swap(x,y);
    ans+=ask(1,n,1,id[x],id[y]);
    return ans;
}
int main()
{
    n=read();
    for(int i=1;i<n;i++)
      {
          int x=read(),y=read();
          add(x,y);
          add(y,x);
      }
    dfs(1,0);
    dfs1(1,1);
    for(int i=1;i<=n;i++)
      {
          int x=read();
          printf("%d\n",ASK(1,x));
          change(1,n,1,id[x]);
      }
    return 0;
}
```
