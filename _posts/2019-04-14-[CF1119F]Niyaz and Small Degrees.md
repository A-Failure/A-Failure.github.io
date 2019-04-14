---
layout:		post
title:		"[CF1119F]Niyaz and Small Degrees"
date:		2019-04-14
author:		"Dispwnl"
header-img:	"img/used/"
catalog:	false
tags:
    - 平衡树
    - 贪心
---

## [题目](http://codeforces.com/problemset/problem/1119/F)

### 题目大意

给定一棵$n$个节点的树，每条边有一个权值，求出使每个点度数不超过$0\sim n-1​$删掉的边的权值和最小是多少

### Examples

#### input

```plain
5
1 2 1
1 3 2
1 4 3
1 5 4
```

#### output

```plain
10 6 3 1 0 
```

#### input

```plain
5
1 2 1
2 3 2
3 4 5
4 5 14
```

#### output

```plain
22 6 0 0 0 
```

### 题解

对于某个度数$D$可以$O(n\log n)$求解，$dfs$遍历一遍，$f_{i,0/1}$表示$i$节点父亲边是否选的最小代价，$du_i$表示$i$点的度数，每个节点用堆贪心，如果$f_{son_i,1}+dis(i,son_i)-f_{son_i,0}$为负则优先选并$du_i-1$，然后取前$du_i-D$个即可

发现这个方法$du_i\le D$时$i$号点的选择不会对答案产生贡献，所以可以把$dis(i,x)$加入到$x$的堆里然后删掉$i$，然后对每个度数$>D$的做上面的贪心即可

堆维护好麻烦啊直接写平衡树了

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdlib>
# include<cstdio>
# include<vector>
# include<algorithm>
# define X first
# define Y second 
# define mp make_pair
# define LL long long
# define Pi pair<int,int> 
using namespace std;
const int MAX=2.5e5+5,N=2e6+5;
int n,tot,Top,num;
LL ans;
int rt[MAX],du[MAX],nxt[MAX],h[MAX],use[MAX];
LL f[2][MAX];
struct Fhq_Treap{
	int pos[N],siz[N];
	LL val[N],sum[N];
	int son[N][2];
	void pus(int x) {sum[x]=sum[son[x][0]]+sum[son[x][1]]+val[x],siz[x]=siz[son[x][0]]+siz[son[x][1]]+1;}
	int merge(int x,int y)
	{
		if(!x||!y) return x+y;
		if(pos[x]<pos[y]) return son[x][1]=merge(son[x][1],y),pus(x),x;
		return son[y][0]=merge(x,son[y][0]),pus(y),y;
	}
	void split(int x,LL k,int &a,int &b)
	{
		if(!x) return void(a=b=0);
		if(k<val[x]) b=x,split(son[x][0],k,a,son[x][0]);
		else a=x,split(son[x][1],k,son[x][1],b);
		pus(x);
	}
	LL ask(int x,int k)
	{
		if(!x||k<=0) return 0;
		int tt=siz[son[x][0]]+1;
		if(k==tt) return sum[son[x][0]]+val[x];
		if(k<tt) return ask(son[x][0],k);
		return sum[son[x][0]]+val[x]+ask(son[x][1],k-tt);
	}
	void ins(int x,LL v)
	{
		int now=++tot,a,b;
		sum[now]=val[now]=v,siz[now]=1,pos[now]=rand(),split(rt[x],v,a,b),rt[x]=merge(a,merge(now,b));
	}
	void del(int x,LL v)
	{
		int a,b,c,d;
		split(rt[x],v,a,b),split(a,v-1,c,d),d=merge(son[d][0],son[d][1]),rt[x]=merge(merge(c,d),b);
	}
}Tree;
vector<int> vc[MAX];
vector<Pi> vec[MAX];
int read()
{
	int x(0);
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
void Del(int x)
{
	for(int i=0,cnt=vec[x].size();i<cnt;++i)
	  Tree.ins(vec[x][i].X,vec[x][i].Y);
}
bool cmp(Pi a,Pi b) {return du[a.X]<du[b.X];}
void dfs(int x,int k)
{
	use[x]=k;
	int cnt=vec[x].size(),K=du[x]-k;
	LL Sum=f[0][x]=f[1][x]=0;
	vector<LL> Vec;
	while(h[x]<cnt&&du[vec[x][h[x]].X]<=k) ++h[x];
	for(int i=h[x],y;i<cnt;++i)
	  if(use[y=vec[x][i].X]!=k)
	  {
	  	dfs(y,k);
	  	if(f[1][y]+vec[x][i].Y<=f[0][y]) f[0][x]+=f[1][y]+vec[x][i].Y,f[1][x]+=f[1][y]+vec[x][i].Y,--K;
	  	else Tree.ins(x,f[1][y]+vec[x][i].Y-f[0][y]),Sum+=f[0][y],Vec.push_back(f[1][y]+vec[x][i].Y-f[0][y]);
	  }
	f[0][x]+=Sum+Tree.ask(rt[x],K),f[1][x]+=Sum+Tree.ask(rt[x],K-1);
	for(int i=0,cnt=Vec.size();i<cnt;++i)
	  Tree.del(x,Vec[i]);
}
int main()
{
	n=read();
	for(int i=1,x,y,d;i<n;++i)
	  x=read(),y=read(),vec[x].push_back(mp(y,d=read())),vec[y].push_back(mp(x,d)),++du[x],++du[y],ans+=d;
	for(int i=1;i<=n;++i)
	  vc[du[i]].push_back(i),sort(vec[i].begin(),vec[i].end(),cmp);
	printf("%I64d ",ans),nxt[n]=n;
	for(int i=n-1;i>=0;--i)
	  if(vc[i+1].size()) nxt[i]=i+1;
	  else nxt[i]=nxt[i+1];
	for(int x=1;x<n-1;++x)
	  {
	  	ans=0;
	  	for(int i=0,cnt=vc[x].size();i<cnt;++i)
	  	  Del(vc[x][i]);
	  	for(int i=x+1;i<n;i=nxt[i])
	  	  for(int j=0,cnt=vc[i].size();j<cnt;++j)
	  		if(use[vc[i][j]]!=x) dfs(vc[i][j],x),ans+=f[0][vc[i][j]];
	  	printf("%I64d ",ans);
	  }
	return printf("0 "),0;
}
```

