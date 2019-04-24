---
layout:		post
title:		"[ARC073F]Many Moves"
date:		2019-04-24
author:		"Dispwnl"
header-img:	"img/used/1517062.png"

---

## [题目]()

### 题目大意

在一行中有$n$个格子，从左往右编号为$1$到$n$。

有$2$颗棋子，一开始分别位于位置$A$和$B$。按顺序给出$Q$个要求，每个要求是如下形式：

- 给出一个位置$x_i$，要求将两个棋子中任意一个移动到位置$x_i$。

将一颗棋子移动一格需要花费$1$秒，就是说将棋子从$X$位置移动到$Y$位置需要花费$\vert X-Y\vert $秒。

为了回答要求，你只能移动棋子，并且同一时刻只能移动一颗棋子。要求的顺序是不可更改的。在同一时间允许两颗棋子在同一个格子内。

### Sample Input 1

```plain
8 3 1 8
3 5 1
```

### Sample Output 1

```plain
7
```


### Sample Input 2


```plain
9 2 1 9
5 1
```

### Sample Output 2


```plain
4
```

### Sample Input 3

```plain
9 2 1 9
5 9
```

### Sample Output 3


```plain
4
```
### Sample Input 4


```plain
11 16 8 1
1 1 5 1 11 4 5 2 5 3 3 3 5 5 6 7
```

### Sample Output 4


```plain
21
```

### 题解

看到有两个棋子移动就想到设状态$f_{i,j}$表示第$i$次移动不动的棋子在$j$位置的最小代价，则有转移

$f_{i,j}=f_{i-1,j}+\vert x_i-x_{i-1} \vert,f_{i,x_{i-1}}=\min(f_{i-1,j}+\vert j-x_i\vert)$

线段树维护即可

> 取值用<code>int</code>，爆负两行泪

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdlib>
# include<cstdio>
# include<algorithm>
# define tl (k<<1)
# define tr (k<<1|1)
# define mid (l+r>>1)
# define LL long long
using namespace std;
const int MAX=2e5+5;
const LL inf=1e14;
struct p{
	LL x,xup,xdn,tags;
	p(LL X=0,LL XU=0,LL XD=0,LL TAG=0) {x=X,xup=XU,xdn=XD,tags=TAG;}
}s[MAX<<2];
int n,Q,A,B;
int X[MAX];
int read()
{
	int x(0);
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
p min(p a,p b) {return p(min(a.x,b.x),min(a.xup,b.xup),min(a.xdn,b.xdn));}
void pus(int k) {s[k]=min(s[tl],s[tr]);}
void Add(int k,LL d) {s[k].tags+=d,s[k].x+=d,s[k].xup+=d,s[k].xdn+=d;}
void down(int k) {if(s[k].tags) Add(tl,s[k].tags),Add(tr,s[k].tags),s[k].tags=0;}
void change(int l,int r,int k,int x,LL d)
{
	if(l==r) {d=min(d,inf),s[k].x=d,s[k].xup=d+l,s[k].xdn=d-l;return;}
	down(k);
	if(x<=mid) change(l,mid,tl,x,d);
	else change(mid+1,r,tr,x,d);
	pus(k);
}
void change(int l,int r,int k,int L,int R,int d)
{
	if(r<L||R<l) return;
	if(l>=L&&r<=R) return void(Add(k,d));
	down(k),change(l,mid,tl,L,R,d),change(mid+1,r,tr,L,R,d),pus(k);
}
LL ask(int l,int r,int k,int L,int R)
{
	if(r<L||R<l) return inf;
	if(l>=L&&r<=R) return s[k].x;
	return down(k),min(ask(l,mid,tl,L,R),ask(mid+1,r,tr,L,R));
}
LL ask_up(int l,int r,int k,int L,int R)
{
	if(r<L||R<l) return inf;
	if(l>=L&&r<=R) return s[k].xup;
	return down(k),min(ask_up(l,mid,tl,L,R),ask_up(mid+1,r,tr,L,R));
}
LL ask_down(int l,int r,int k,int L,int R)
{
	if(r<L||R<l) return inf;
	if(l>=L&&r<=R) return s[k].xdn;
	return down(k),min(ask_down(l,mid,tl,L,R),ask_down(mid+1,r,tr,L,R));
}
void build(int l=1,int r=n,int k=1)
{
	s[k]=p(inf,inf,inf);
	if(l==r) return;
	build(l,mid,tl),build(mid+1,r,tr);
}
int main()
{
	n=read(),Q=read(),A=read(),B=read(),build();
	for(int i=1;i<=Q;++i)
	  {
	  	X[i]=read();
	  	if(i==1) change(1,n,1,A,abs(B-X[i])),change(1,n,1,B,abs(A-X[i]));
		else
		{
			LL Lv=ask_down(1,n,1,1,X[i])+X[i],Rv=ask_up(1,n,1,X[i]+1,n)-X[i],Xv=ask(1,n,1,X[i-1],X[i-1]);
			change(1,n,1,X[i-1],min(Xv+abs(X[i]-X[i-1]),min(Lv,Rv))),change(1,n,1,1,X[i-1]-1,abs(X[i]-X[i-1])),change(1,n,1,X[i-1]+1,n,abs(X[i]-X[i-1]));
		}
	  }
	LL ans=1e18;
	for(int i=1;i<=n;++i)
	  ans=min(ans,ask(1,n,1,i,i));
	return printf("%lld",ans),0;
}
```

