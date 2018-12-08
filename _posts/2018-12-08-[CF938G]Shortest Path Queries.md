---
layout:         post
title:          "[CF938G]Shortest Path Queries"
date:           2018-12-08
auther:         "Dispwnl"
header-img:     "img/used/1075807.jpg"
catalog: true
tags:
    - 线段树
    - 线性基
    - 并查集
---
## [题目](http://codeforces.com/contest/938/problem/G)
### 题目大意
给定一个$n$个点$m$条边无向图，有$q$个询问

- $1\;x\;y\;d$ 新加入一条连接$x,y$长度为$d$的无向边
- $2\;x\;y$删除$x,y$之间的边
- $3\;x\;y$查询$x,y$之间的最小$XOR$路径

### Example
#### input
```
5 5
1 2 3
2 3 4
3 4 5
4 5 6
1 5 1
5
3 1 5
1 1 3 1
3 1 5
2 1 5
3 1 5
```
#### output
```
1
1
2
```

### 题解
每条边存在的时间是一个区间，所以可以用线段树分治搞

先不管加入删除操作，怎么查询$x,y$的最小$XOR$路径呢？

分情况讨论：

- 如果$x\rightarrow y$的路径上没有环，那么$XOR_{x,y}$就是路径上每条边的$XOR$和
- 如果$x\rightarrow y$的路径上有环，对于每个环，沿环遍历一遍得到的影响可以通过再遍历一遍环来取消

所以对于每个环，可以选或不选，所以可以用线性基维护

维护图的生成树，如果加入一条非树边（即生成了环），就把这个环的$XOR$和扔进线性基

维护生成树用并查集即可，遍历完这个区间就撤销合并操作，所以不能路径压缩，按秩合并复杂度$O(logn)$

这样总复杂度就是$O(nlog^2n)$的

### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<vector>
# include<stack>
# include<map>
# include<algorithm>
# define tl (k<<1)
# define tr (k<<1|1)
# define mid (l+r>>1)
using namespace std;
const int MAX=4e5+5;
struct p{
	int x,y,d;
}qu[MAX];
struct q{
	int x,d;
	bool operator< (const q &a)
	const{
		if(x!=a.x) return x<a.x;
		return d<a.d;
	}
}qaq[MAX];
struct u{
	int x,y,s1;
}st[MAX<<1];
int n,m,Q,cnt,cnt1,Top;
int fa[MAX],w[MAX],siz[MAX],ans[MAX];
vector<int> s[MAX<<2];
map<q,q> ma;
struct Base{
	int P[31];
	void ins(int x)
	{
		for(int i=30;i>=0;--i)
		  if(x&(1<<i))
		  {
		  	if(!P[i]) {P[i]=x;break;}
		  	x^=P[i];
		  }
	}
	int ask(int x)
	{
		for(int i=30;i>=0;--i)
		  x=min(x,x^P[i]);
		return x;
	}
}A;
int read()
{
	int x(0);
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
int find(int x)
{
	while(x!=fa[x]) x=fa[x];
	return x;
}
int GET_XOR(int x)
{
	int ans=0;
	while(x!=fa[x]) ans^=w[x],x=fa[x];
	return ans;
}
void change(int l,int r,int k,int L,int R,int id)
{
	if(L>R) return;
	if(l==L&&r==R) return void(s[k].push_back(id));
	if(R<=mid) change(l,mid,tl,L,R,id);
	else if(L>mid) change(mid+1,r,tr,L,R,id);
	else change(l,mid,tl,L,mid,id),change(mid+1,r,tr,mid+1,R,id);
}
void Solve(int l=1,int r=Q,int k=1,Base B=A)
{
	int top=Top;
	for(int i=0,r1,r2,cnt=s[k].size();i<cnt;++i)
	  {
	  	p tt=qu[s[k][i]];
	  	r1=find(tt.x),r2=find(tt.y);
	  	if(r1!=r2)
		{
	  		if(siz[r1]>siz[r2]) swap(r1,r2),swap(tt.x,tt.y);
	  		st[++Top]=(u){r1,r2,siz[r2]},siz[r2]+=siz[r1],fa[r1]=r2,w[r1]=GET_XOR(tt.x)^GET_XOR(tt.y)^tt.d;
		}
		else B.ins(GET_XOR(tt.x)^GET_XOR(tt.y)^tt.d);
	  }
	if(l==r)
	{
		if(qaq[l].x) ans[l]=B.ask(GET_XOR(qaq[l].x)^GET_XOR(qaq[l].d));
	}
	else Solve(l,mid,tl,B),Solve(mid+1,r,tr,B);
	u tt;
	while(Top!=top) tt=st[Top--],fa[tt.x]=tt.x,siz[tt.y]=tt.s1,w[tt.x]=0;
}
int main()
{
	n=read(),m=read();
	memset(ans,-1,sizeof(ans));
	for(int i=1,x,y,d;i<=m;++i)
	  {
	  	x=read(),y=read(),d=read();
	  	if(x>y) swap(x,y);
	  	ma[(q){x,y}]=(q){1,d};
	  }
	Q=read();
	for(int i=1,op,x,y;i<=Q;++i)
	  {
	  	op=read(),x=read(),y=read();
	  	if(x>y) swap(x,y);
		if(op==1) ma[(q){x,y}]=(q){i,read()};
	  	else if(op==2)
	  	{
	  		q tt=ma[(q){x,y}];
			ma.erase((q){x,y}),qu[++cnt]=(p){x,y,tt.d},change(1,Q,1,tt.x,i,cnt);
		}
		else if(op==3) qaq[i]=(q){x,y};
	  }
	for(map<q,q>::iterator A=ma.begin();A!=ma.end();++A)
	  {
	  	q t1=(*A).first,t2=(*A).second;
	  	qu[++cnt]=(p){t1.x,t1.d,t2.d},change(1,Q,1,t2.x,Q,cnt);
	  }
	for(int i=1;i<=n;++i)
	  fa[i]=i,siz[i]=1;
	Solve();
	for(int i=1;i<=Q;++i)
	  if(ans[i]!=-1) printf("%d\n",ans[i]);
	return 0;
}
```
