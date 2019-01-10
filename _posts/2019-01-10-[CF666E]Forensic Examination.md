---
layout:		post
title:		"[CF666E]Forensic Examination"
date:		2019-01-10
author:		"Dispwnl"
header-img:	"img/used/432.jpg"
catalog:	false
tags:
    - 后缀自动机
    - 线段树
---
## [题目](http://codeforces.com/contest/666/problem/E)

### 题目大意

给定一个字符串$S$和$m$个字符串$T[1...m]$，给定$q$个询问，每个询问给定四个整数$l,r,p_l,p_r$，然后查询$S[p_l...p_r]$在$T[l...r]$的某个串中最多出现的次数和最多出现的$T$的编号

### Examples

#### input

```
suffixtree
3
suffixtreesareawesome
cartesiantreeisworsethansegmenttree
nyeeheeheee
2
1 2 1 10
1 3 9 10
```

#### output

```
1 1
3 4
```

### 题解

先建立出$T$串的广义后缀自动机，同时对$SAM$每个节点染色，考虑查询的子串不变的情况，这样只需要在$SAM$上匹配子串，如果能匹配到最后，只需要统计当前点的子树中数量最多的颜色是哪个和数量是多少就可以了，用线段树合并上去即可

现在要查询的是$S$上的一段区间，假设只有一个询问，在$SAM$上先匹配前缀，如果匹配不到就跳$father$，这就能匹配从这个点往前的最长子串了

所以只用找这个点结束、子串长度$=p_r-p_l+1$的状态就可以了，所以可以从当前点跳$father$（$father$也是从这个点往前但是长度更小）找到这个点，然后线段树合并查询就行了

对于多组询问，把询问按右端点拍下序离线询问即可

因为空间十分充裕，可以合并的时候新开节点，这样就不用询问挂树上然后$dfs$查询了~~只不过时间和空间都不优越~~

本来用的基数排序……然后一直不知道为什么会错QAQ

然后$fuge$告诉我广义$SAM$不能用基数排序因为会合并不同串的信息balabala……

![](/img/qaq/346.jpg)

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
# define tl s[k].l
# define tr s[k].r
# define mid (l+r>>1)
# define X first
# define Y second
# define mp make_pair
using namespace std;
const int MAX=1e5+5,N=1e6+5;
struct p{
	int l,r;
	pair<int,int> x;
}s[MAX<<5];
struct q{
	int l,r,_l,_r,id;
	bool operator< (const q &a)
	const{
		return _r<a._r;
	}
}qu[N];
int n,m,tot,Q;
int rt[MAX];
char a[MAX],b[N];
pair<int,int> ans[N];
void pus(int k) {s[k].x=max(s[tl].x,s[tr].x);}
void change(int l,int r,int &k,int x)
{
	if(!k) k=++tot;
	if(l==r)
	{
		++s[k].x.X;
		return void(s[k].x.Y=-l);
	}
	if(x<=mid) change(l,mid,tl,x);
	else change(mid+1,r,tr,x);
	pus(k);
}
pair<int,int> ask(int l,int r,int k,int L,int R)
{
	if(r<L||R<l||!k) return mp(0,-1e9);
	if(l>=L&&r<=R) return s[k].x;
	return max(ask(l,mid,tl,L,R),ask(mid+1,r,tr,L,R));
}
int merge(int x,int y)
{
	if(!x||!y) return x+y;
	int k=++tot;
	if(!s[x].l&&!s[x].r) s[k]=s[x],s[k].x.X+=s[y].x.X;
	else tl=merge(s[x].l,s[y].l),tr=merge(s[x].r,s[y].r),pus(k);
	return k;
}
struct SAM{
	struct p{
		int x,y;
	}c[MAX];
	int l,r,L,num;
	int h[MAX],len[MAX],fa[MAX];
	int son[MAX][26],f[19][MAX];
	SAM() {l=r=1;}
	void ins(int x,int id)
	{
		int tt=r;
		len[r=++l]=++L,change(1,m,rt[r],id);
		for(;tt&&!son[tt][x];tt=fa[tt])
		  son[tt][x]=r;
		if(!tt) return void(fa[r]=1);
		int qwq=son[tt][x];
		if(len[qwq]==len[tt]+1) return void(fa[r]=qwq);
		len[++l]=len[tt]+1,fa[l]=fa[qwq],fa[qwq]=fa[r]=l;
		for(int i=0;i<26;++i)
		  son[l][i]=son[qwq][i];
		for(int i=tt;son[i][x]==qwq;i=fa[i])
		  son[i][x]=l;
	}
	void add(int x,int y) {c[++num]=(p){h[x],y},h[x]=num;}
	void dfs(int x=1)
	{
		f[0][x]=fa[x];
		for(int i=h[x];i;i=c[i].x)
		  dfs(c[i].y),rt[x]=merge(rt[x],rt[c[i].y]);
	}
	void Init()
	{
		for(int i=2;i<=l;++i)
		  add(fa[i],i);
		dfs();
		for(int i=1;i<19;++i)
		  for(int j=1;j<=l;++j)
		    f[i][j]=f[i-1][f[i-1][j]];
	}
	void Solve()
	{
		for(int i=1,x=1,r=1,_L=0;i<=Q;++i)
		  {
		  	while(r<=qu[i]._r)
		  	{
		  		if(son[x][b[r]-'a']) x=son[x][b[r]-'a'],++_L;
		  		else
		  		{
		  			while(x&&!son[x][b[r]-'a']) x=fa[x];
		  			if(!x) x=1,_L=0;
		  			else _L=len[x]+1,x=son[x][b[r]-'a'];
				}
				++r;
			}
			if(_L>=qu[i]._r-qu[i]._l+1)
			{
				int L,_x=x;
				for(L=18;L>=0;--L)
				  if(len[f[L][_x]]>=qu[i]._r-qu[i]._l+1) _x=f[L][_x];
				ans[qu[i].id]=ask(1,m,rt[_x],qu[i].l,qu[i].r);
			}
			if(!ans[qu[i].id].X) ans[qu[i].id].Y=-qu[i].l;
		  }
		for(int i=1;i<=Q;++i)
		  printf("%d %d\n",-ans[i].Y,ans[i].X);
	}
}_S;
int main()
{
	scanf("%s%d",b+1,&m),n=strlen(b+1);
	for(int i=1,_n;i<=m;++i,_S.L=0,_S.r=1)
	  {
	  	scanf("%s",a+1),_n=strlen(a+1);
	  	for(int j=1;j<=_n;++j)
	  	  _S.ins(a[j]-'a',i);
	  }
	_S.Init(),scanf("%d",&Q);
	for(int i=1;i<=Q;++i)
	  scanf("%d%d%d%d",&qu[i].l,&qu[i].r,&qu[i]._l,&qu[i]._r),qu[i].id=i;
	return sort(qu+1,qu+1+Q),_S.Solve(),0;
}
```

