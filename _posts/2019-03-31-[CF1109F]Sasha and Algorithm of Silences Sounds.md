---
layout:		post
title:		"[CF1109F]Sasha and Algorithm of Silence's Sounds"
date:		2019-03-31
author:		"Dispwnl"
header-img:	"img/used/79000.jpg"
catalog:	false
tags:
    - Link Cut Tree
    - 线段树
---

## [题目](http://codeforces.com/problemset/problem/1109/F)

### 题目大意

给定一个矩阵，矩阵中每个数为$1\sim n\times m$的整数且互不相同，问有多少个区间$[l,r]$，使得值在$l\sim r$之间的格子相连（四联通）能形成一棵树

### Examples

#### input

```plain
1 5
1 2 3 4 5
```

#### output

```plain
15
```

#### input

```plain
2 3
1 2 3
4 5 6
```

#### output

```plain
15
```

#### input

```plain
4 4
4 3 2 16
1 13 14 15
5 7 8 12
6 11 9 10
```

#### output

```plain
50
```

### 题解

先有一个性质：

> 设$L_r$为以$r$为区间右端点能形成的合法区间（指无环图）的最左端点，有$L_r\le L_{r+1}$

如果$L_r>L_{r+1}$并且区间合法，$[L_r,r]$是包含在$[L_{r+1},r+1]$中的，那么$[L_{r+1},r]$肯定也是合法区间（无环图删掉一些边还是无环图），与$L$的定义不符，所以不成立

这样可以枚举右端点，左端点拿个指针维护，判断是否合法可以用$LCT$维护

现在知道某个区间合法，如何统计它的答案呢？

用线段树维护区间$[L_r,r]$中每个位置到$r$形成的联通块个数，即点数减去边数，这样位置$p$加入一条边的时候区间$[L_r,p]$的值都要$-1$，移动到下一个$r$的时候区间$[L_r,r]$的值都要$+1​$


### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
# define tl (k<<1)
# define tr (k<<1|1)
# define mid (l+r>>1)
# define LL long long
# define pu(x,y) (x-1)*m+y
using namespace std;
const int MAX=2e5+5;
int n,m,tot,Top,N;
LL ans;
int a[MAX],id[MAX];
struct Link_Cut_Tree{
	int fa[MAX],st[MAX];
	int son[MAX][2];
	bool fl[MAX];
	bool is_root(int x) {return son[fa[x]][0]!=x&&son[fa[x]][1]!=x;}
	bool GET_ID(int x) {return son[fa[x]][1]==x;}
	void down(int x)
	{
		if(fl[x]&&x)
		{
			if(son[x][0]) fl[son[x][0]]^=1;
			if(son[x][1]) fl[son[x][1]]^=1;
			fl[x]=0,swap(son[x][0],son[x][1]);
		}
	}
	void rot(int x)
	{
		int y=fa[x],z=fa[y],k=GET_ID(x);
		if(!is_root(y)) son[z][GET_ID(y)]=x;
		fa[x]=z,fa[y]=x,fa[son[x][k^1]]=y,son[y][k]=son[x][k^1],son[x][k^1]=y;
	}
	void splay(int x)
	{
		int qwq=x;
		for(;!is_root(qwq);qwq=fa[qwq])
		  st[++Top]=qwq;
		down(qwq);
		while(Top) down(st[Top--]);
		for(int y;!is_root(x);rot(x))
		  if(!is_root(y=fa[x])) rot(GET_ID(x)==GET_ID(y)?y:x);
	}
	void access(int x)
	{
		for(int y=0;x;y=x,x=fa[x])
		  splay(x),son[x][1]=y;
	}
	void make_root(int x) {access(x),splay(x),fl[x]^=1;}
	void split(int x,int y) {make_root(x),access(y),splay(y);}
	int GET_ROOT(int x)
	{
		access(x),splay(x);
		while(son[x][0]) x=son[x][0];
		return splay(x),x;
	}
	void link(int x,int y) {make_root(x),fa[x]=y;}
	void cut(int x,int y)
	{
		split(x,y);
		if(son[y][0]==x) son[y][0]=fa[x]=0;
	}
}Tree;
struct p{
	int x,siz,tag;
}s[MAX<<2];
p pus(p b,p c)
{
	p a;
	if(b.x==c.x) a.x=b.x,a.siz=b.siz+c.siz;
	else if(b.x<c.x) a=b;
	else a=c;
	return a.tag=0,a;
}
void build(int l=1,int r=N,int k=1)
{
	s[k].x=1,s[k].siz=r-l+1;
	if(l==r) return;
	build(l,mid,tl),build(mid+1,r,tr);
}
void Upt_val(int k,int dis) {s[k].x+=dis,s[k].tag+=dis;}
void down(int k)
{
	if(s[k].tag)
	{
		int F=s[k].tag;
		s[k].tag=0,Upt_val(tl,F),Upt_val(tr,F);
	}
}
void change(int l,int r,int k,int L,int R,int dis)
{
	if(r<L||R<l) return;
	if(l>=L&&r<=R) return void(Upt_val(k,dis));
	down(k),change(l,mid,tl,L,R,dis),change(mid+1,r,tr,L,R,dis),s[k]=pus(s[tl],s[tr]);
}
p ask(int l,int r,int k,int L,int R)
{
	if(l==L&&r==R) return s[k];
	down(k);
	if(R<=mid) return ask(l,mid,tl,L,R);
	if(L>mid) return ask(mid+1,r,tr,L,R);
	return pus(ask(l,mid,tl,L,mid),ask(mid+1,r,tr,mid+1,R));
}
int read()
{
	int x(0);
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
bool look(int x,int l,int r)
{
	int Y=id[x]%m,X=(id[x]-Y)/m+1;
	if(!Y) Y=m,--X;
	if(X>1&&a[pu(X-1,Y)]>=l&&a[pu(X-1,Y)]<=r)
	{
		if(X<n&&a[pu(X+1,Y)]>=l&&a[pu(X+1,Y)]<=r) if(Tree.GET_ROOT(pu(X-1,Y))==Tree.GET_ROOT(pu(X+1,Y))) return 1;
		if(Y>1&&a[pu(X,Y-1)]>=l&&a[pu(X,Y-1)]<=r) if(Tree.GET_ROOT(pu(X-1,Y))==Tree.GET_ROOT(pu(X,Y-1))) return 1;
		if(Y<m&&a[pu(X,Y+1)]>=l&&a[pu(X,Y+1)]<=r) if(Tree.GET_ROOT(pu(X-1,Y))==Tree.GET_ROOT(pu(X,Y+1))) return 1;
	}
	if(X<n&&a[pu(X+1,Y)]>=l&&a[pu(X+1,Y)]<=r)
	{
		if(Y>1&&a[pu(X,Y-1)]>=l&&a[pu(X,Y-1)]<=r) if(Tree.GET_ROOT(pu(X+1,Y))==Tree.GET_ROOT(pu(X,Y-1))) return 1;
		if(Y<m&&a[pu(X,Y+1)]>=l&&a[pu(X,Y+1)]<=r) if(Tree.GET_ROOT(pu(X+1,Y))==Tree.GET_ROOT(pu(X,Y+1))) return 1;
	}
	if(Y>1&&a[pu(X,Y-1)]>=l&&a[pu(X,Y-1)]<=r) if(Y<m&&a[pu(X,Y+1)]>=l&&a[pu(X,Y+1)]<=r) if(Tree.GET_ROOT(pu(X,Y-1))==Tree.GET_ROOT(pu(X,Y+1))) return 1;
	return 0;
}
void ADD(int x,int l,int r)
{
	int Y=id[x]%m,X=(id[x]-Y)/m+1;
	if(!Y) Y=m,--X;
	if(X>1&&a[pu(X-1,Y)]>=l&&a[pu(X-1,Y)]<=r) Tree.link(pu(X-1,Y),id[x]),change(1,N,1,l,a[pu(X-1,Y)],-1);
	if(X<n&&a[pu(X+1,Y)]>=l&&a[pu(X+1,Y)]<=r) Tree.link(pu(X+1,Y),id[x]),change(1,N,1,l,a[pu(X+1,Y)],-1);
	if(Y>1&&a[pu(X,Y-1)]>=l&&a[pu(X,Y-1)]<=r) Tree.link(pu(X,Y-1),id[x]),change(1,N,1,l,a[pu(X,Y-1)],-1);
	if(Y<m&&a[pu(X,Y+1)]>=l&&a[pu(X,Y+1)]<=r) Tree.link(pu(X,Y+1),id[x]),change(1,N,1,l,a[pu(X,Y+1)],-1);
}
void CUT(int x,int l,int r)
{
	int Y=id[x]%m,X=(id[x]-Y)/m+1;
	if(!Y) Y=m,--X;
	if(X>1&&a[pu(X-1,Y)]>=l&&a[pu(X-1,Y)]<=r) Tree.cut(pu(X-1,Y),id[x]);
	if(X<n&&a[pu(X+1,Y)]>=l&&a[pu(X+1,Y)]<=r) Tree.cut(pu(X+1,Y),id[x]);
	if(Y>1&&a[pu(X,Y-1)]>=l&&a[pu(X,Y-1)]<=r) Tree.cut(pu(X,Y-1),id[x]);
	if(Y<m&&a[pu(X,Y+1)]>=l&&a[pu(X,Y+1)]<=r) Tree.cut(pu(X,Y+1),id[x]);
}
int main()
{
	n=read(),m=read(),N=n*m,build();
	for(int i=1;i<=N;++i)
	  id[a[i]=read()]=i;
	for(int i=1,L=1;i<=N;++i)
	  {
	  	while(look(i,L,i-1)) CUT(L,L+1,i),++L;
	  	ADD(i,L,i-1);
	  	p x=ask(1,N,1,L,i);
	  	if(x.x==1) ans+=x.siz;
	  	change(1,N,1,L,i,1);
	  }
	return printf("%I64d",ans),0;
}
```

