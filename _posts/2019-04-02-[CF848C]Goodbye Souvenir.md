---
layout:		post
title:		"[CF848C]Goodbye Souvenir"
date:		2019-04-02
author:		"Dispwnl"
header-img:	"img/used/78787.jpg"
catalog:	false
tags:
    - CDQ分治
---

## [题目](<https://codeforces.com/contest/848/problem/C>)

### 题目大意

给定长度为$n​$的数组, 定义数字$X​$在$[l,r]​$内的值为数字$X​$在$[l,r]​$内最后一次出现位置的下标减去第一次出现位置的下标
给定$m​$次询问, 每次询问有三个整数$a, b, c​$, 询问规则如下:
当$a = 1​$时, 将数组内第$b​$个元素更改为$c​$
当$a = 2​$时, 求区间$[b,c]​$所有数字的值的和

### Examples

#### input

```plain
7 6
1 2 3 1 3 2 1
2 3 7
2 1 3
1 7 2
1 3 2
2 1 6
2 5 7
```

#### output

```plain
5
0
7
1
```

#### input

```plain
7 5
1 3 2 1 4 2 3
1 1 4
2 2 3
1 1 7
2 4 5
1 1 7
```

#### output

```plain
0
0
```

### 题解

处理出来每个点前面第一个相等的点的位置$pre_i$，这样可以把一个点定义为$(pre_i,i)$，点权定义为$i-pre_i$，每个询问就是查询矩阵$(l,l),(r,r)$中的点值和

对于每个修改，影响到的只有$b$位置，$b$后面第一个$c$，$b$后面第一个$a_b$，拿个<code>set</code>维护即可

然后用$CDQ$分治处理即可

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<set>
# include<algorithm>
# define LL long long
# define mid (l+r>>1)
using namespace std;
const int MAX=1e5+5;
struct p{
	int id,x,y,v,fl;
}qu[MAX<<3];
int n,m,tot,cnt;
int val[MAX],id[MAX<<3],_id[MAX<<3],L[MAX],pre[MAX];
LL s[MAX],ans[MAX];
set<int> S[MAX];
int read()
{
	int x(0);
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
int lowbit(int x) {return x&(-x);}
LL ask(int x)
{
	LL ans=0;
	for(int i=x;i;i-=lowbit(i))
	  ans+=s[i];
	return ans;
}
void change(int x,int d)
{
	for(int i=x;i<=n;i+=lowbit(i))
	  s[i]+=d;
}
void Solve(int l=1,int r=tot)
{
	if(l==r) return;
	Solve(l,mid),Solve(mid+1,r);
	int L=l;
	for(int i=mid+1;i<=r;++i)
	  if(qu[id[i]].id)
	  {
	  	for(;L<=mid&&qu[id[L]].x<=qu[id[i]].x;++L)
	  	  if(!qu[id[L]].id) change(qu[id[L]].y,qu[id[L]].v);
	  	ans[qu[id[i]].id]+=qu[id[i]].fl*ask(qu[id[i]].y);
	  }
	for(int i=l;i<L;++i)
	  if(!qu[id[i]].id) change(qu[id[i]].y,-qu[id[i]].v);
	for(int i=l,L=l,R=mid+1;i<=r;++i)
	  if((qu[id[L]].x<qu[id[R]].x&&L<=mid)||R>r) _id[i]=id[L++];
	  else _id[i]=id[R++];
	for(int i=l;i<=r;++i)
	  id[i]=_id[i];
}
int main()
{
	n=read(),m=read();
	for(int i=1;i<=n;++i)
	  {
	  	val[i]=read(),pre[i]=L[val[i]],L[val[i]]=i,S[val[i]].insert(i);
		if(pre[i]) qu[++tot]=(p){0,pre[i],i,i-pre[i]};
	  }
	for(int i=1,a,b,c,x;i<=m;++i)
	  {
	  	a=read(),b=read(),c=read();
	  	if(a==1)
	  	{
	  		if(c!=val[b])
	  		{
	  			if(pre[b]) qu[++tot]=(p){0,pre[b],b,pre[b]-b};
	  			set<int>::iterator A=S[c].upper_bound(b),B=S[val[b]].upper_bound(b);
	  			if(A!=S[c].end())
	  			{
	  				x=*A;
					if(pre[x]) qu[++tot]=(p){0,pre[x],x,pre[x]-x};
					pre[x]=b,qu[++tot]=(p){0,pre[x],x,x-pre[x]};
				}
				if(B!=S[val[b]].end())
				{
					x=*B,qu[++tot]=(p){0,b,x,b-x},pre[x]=pre[b];
					if(pre[x]) qu[++tot]=(p){0,pre[x],x,x-pre[x]};
				}
				if(A!=S[c].begin()) x=*(--A),pre[b]=x,qu[++tot]=(p){0,pre[b],b,b-pre[b]};
				else pre[b]=0;
				S[val[b]].erase(b),val[b]=c,S[c].insert(b);
			}
		}
		else if(a==2) qu[++tot]=(p){++cnt,c,c,0,1},qu[++tot]=(p){cnt,b-1,c,0,-1},qu[++tot]=(p){cnt,c,b-1,0,-1},qu[++tot]=(p){cnt,b-1,b-1,0,1};
	  }
	for(int i=1;i<=tot;++i)
	  id[i]=i;
	Solve();
	for(int i=1;i<=cnt;++i)
	  printf("%I64d\n",ans[i]);
	return 0;
}
```

