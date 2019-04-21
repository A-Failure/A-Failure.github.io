---
layout:		post
title:		"[CF1140G]Double Tree"
date:		2019-04-15
author:		"Dispwnl"
header-img:	"img/used/60223405.jpg"
catalog:	false
tags:
    - 最短路
    - 倍增
---

## [题目](<https://www.luogu.org/problemnew/show/CF1140G>)

### 题目大意

给出一个$2N$个点、$3N-2$条边的无向图，边有边权。这张图满足以下性质：

①对于图上的一条边$(u,v)(2 \vert u , 2 \vert v)$，一定存在边$(u+1,v+1)$，反之亦然；

②图上存在边$(u,u+1)$

可以知道编号为偶数的点的导出子图和编号为奇数的点的导出子图都是一棵树，且它们同构。

现在给出$Q​$组询问，每组询问询问两个点$x,y​$之间的最短路长度。

### Example

#### input

```plain
5
3 6 15 4 8
1 2 5 4
2 3 5 7
1 4 1 5
1 5 2 1
3
1 2
5 6
1 10
```

#### output

```plain
3
15
4
```

### 题解

用$D(u,v)$表示$u\to v$的最短路，首先可以用一个$SPFA$处理出来$D(u,u+1)$，然后可以处理出来每个点$v$和它父亲$u$的$D(2u,2v),D(2u+1,2v+1),D(2u,2v+1),D(2u+1,2v)$，再用倍增就可以求出每个点到它祖先的上面四个值

对于每个询问直接两边跳到$LCA$然后讨论一下是奇树还是偶树一下就好了

~~整整齐齐的代码~~

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<queue>
# include<algorithm>
# define LL long long
using namespace std;
const int MAX=3e5+5;
struct q{
	int x,y;
	LL d1,d2;
}c[MAX<<1];
struct p{
	LL l___,_l__,__l_,___l;
	p(LL l1=0,LL l2=0,LL l3=0,LL l4=0) {l___=l1,_l__=l2,__l_=l3,___l=l4;}
}F[20][MAX];
int n,Q,num;
int h[MAX],d[MAX];
LL val[MAX];
int f[20][MAX];
bool use[MAX];
queue<int> qu;
LL min(LL a,LL b) {return a<b?a:b;}
p operator+ (p a,p b)
{
	p c;
	c.l___=min(a.l___+b.l___,a._l__+b.__l_);
	c._l__=min(a.l___+b._l__,a._l__+b.___l);
	c.__l_=min(a.___l+b.__l_,a.__l_+b.l___);
	c.___l=min(a.___l+b.___l,a.__l_+b._l__);
	return c;
}
LL read()
{
	LL x(0);
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
void dfs(int x=1,int fa=0)
{
	f[0][x]=fa,d[x]=d[fa]+1;
	for(int i=h[x],y;i;i=c[i].x)
	  if((y=c[i].y)!=fa)
	  {
	  	dfs(y,x);
	  	F[0][y].l___=min(c[i].d1,val[x]+c[i].d2+val[y]);
	  	F[0][y]._l__=min(val[y]+c[i].d2,val[x]+c[i].d1);
	  	F[0][y].__l_=min(c[i].d2+val[x],c[i].d1+val[y]);
	  	F[0][y].___l=min(val[x]+c[i].d1+val[y],c[i].d2);
	  }
}
void add(int x,int y,LL d1,LL d2)
{
	c[++num]=(q){h[x],y,d1,d2},h[x]=num;
	c[++num]=(q){h[y],x,d1,d2},h[y]=num;
}
void Init()
{
	for(int i=1;i<=19;++i)
	  for(int j=1;j<=n;++j)
	    f[i][j]=f[i-1][f[i-1][j]],F[i][j]=F[i-1][j]+F[i-1][f[i-1][j]];
}
void Solve(int ux,int uy)
{
	int x=ux+1>>1,y=uy+1>>1,H;
	if(d[x]<d[y]) swap(x,y),swap(ux,uy);
	p dx=p(0,val[x],val[x],0),dy=p(0,val[y],val[y],0);
	H=d[x]-d[y];
	for(int i=19;i>=0;--i)
	  if(H&(1<<i)) dx=dx+F[i][x],x=f[i][x];
	if(x!=y)
	{
		for(int i=19;i>=0;--i)
		  if(f[i][x]!=f[i][y]) dx=dx+F[i][x],dy=dy+F[i][y],x=f[i][x],y=f[i][y];
		dx=dx+F[0][x],dy=dy+F[0][y];
	}
	if( (ux&1)&& (uy&1)) printf("%I64d\n",min(dx.l___+dy.l___,dx._l__+dy._l__));
	if( (ux&1)&&!(uy&1)) printf("%I64d\n",min(dx.l___+dy.__l_,dx._l__+dy.___l));
	if(!(ux&1)&& (uy&1)) printf("%I64d\n",min(dx.___l+dy._l__,dx.__l_+dy.l___));
	if(!(ux&1)&&!(uy&1)) printf("%I64d\n",min(dx.___l+dy.___l,dx.__l_+dy.__l_));
}
int main()
{
	n=read();
	for(int i=1;i<=n;++i)
	  qu.push(i),val[i]=read();
	for(int i=1,x,y;i<n;++i)
	  {
	  	x=read(),y=read();
	  	LL d1=read(),d2=read();
	  	add(x,y,d1,d2);
	  }
	while(!qu.empty())
	{
		int tt=qu.front();
		qu.pop();
		use[tt]=0;
		for(int i=h[tt],y;i;i=c[i].x)
		  if(val[y=c[i].y]>val[tt]+c[i].d1+c[i].d2)
		  {
		  	val[y]=val[tt]+c[i].d1+c[i].d2;
		  	if(!use[y]) qu.push(y),use[y]=1;
		  }
	}
	dfs(),Init(),Q=read();
	for(int i=1;i<=Q;++i)
	  Solve(read(),read());
	return 0;
}
```

