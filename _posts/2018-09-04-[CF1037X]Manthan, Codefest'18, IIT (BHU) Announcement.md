---
layout:     post
title:      "[CF1037X]Manthan, Codefest'18, IIT (BHU) Announcement"
date:       2018-09-04
author:     "Dispwnl"
header-img: "img/used/2356.jpg"
catalog: true
tags:
    - 比赛
---
>比赛的时候出问题了一直交不上去...气的直接不打了QAQ~~然后听说是手速场233~~

### [A　　Packets](http://codeforces.com/contest/1037/problem/A)
#### 题目大意
你有$n$个硬币，每个硬币价值都为$1$

你要把它们分成若干个小包裹，使得在$1$与$n$之间的所有面额都能用这其中某几个小包裹凑出

每个小包裹只能作为一个整体使用，请求出最少要分几个包裹

#### 题解
裸贪心

#### 代码
```
# include<iostream>
# include<cstdio>
using namespace std;
int n,num;
int main()
{
	scanf("%d",&n);
	int tt=1;
	while(n>tt) n-=tt,++num,tt<<=1;
	if(n>0) ++num;
	return printf("%d",num),0;
}
```

### [B　　Reach Median](http://codeforces.com/contest/1037/problem/B)
#### 题目大意
给定一个长度为$n$的序列和一个整数$s$，$n$一定为奇数

一次操作可以将序列中的某个数$+1$或者$-1$，问至少几次操作可以使这个序列的中位数等于$s$

#### 题解
贪心，排一下序，中间那个数如果大于给定数，就让左边所有大于给定数的数经过减操作变成给定数

如果小于给定数，就让右边所有小于给定数的数经过加操作变成给定数

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
# define LL long long
using namespace std;
const int MAX=2e5+1;
int n,s;
LL ans;
int a[MAX];
int main()
{
	scanf("%d%d",&n,&s);
	for(int i=1;i<=n;++i)
	  scanf("%d",&a[i]);
	sort(a+1,a+1+n);
	int mid=(n+1>>1),ID;
	if(s>a[mid])
	{
		ans+=s-a[mid];
		for(ID=mid+1;ID<=n&&a[ID]<s;++ID)
		  ans+=s-a[ID];
	}
	else if(s<a[mid]) 
	{
		ans+=a[mid]-s;
		for(ID=mid-1;ID>=1&&a[ID]>s;--ID)
		  ans+=a[ID]-s;
	}
	return cout<<ans,0;
}
```
### [C　　Equalize](http://codeforces.com/contest/1037/problem/C)
#### 题目大意
给定两个长度为$n$的$01$序列$a,b$，每次可以执行如下操作：

- 在$a$中选择一个位置$p$，将$a_p$取反，代价是$1$
- 在$a$中选择两个位置$p,q$，将$a_p$和$a_q$互换，代价是$\mid p-q \mid$

要求将$a$变成$b$，最小化代价，$1 \leq n \leq 10^6$

#### 题解
还是贪心...记录下所有不一样的位置，如果要用操作$1$的话，肯定是这两个位置距离$=1$（否则操作$2$替换两次更优）并且不相同

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
const int MAX=1e6+1;
int n,num,ans;
int id[MAX];
string a,b; 
int main()
{
	cin>>n>>a>>b;
	for(int i=0;i<n;++i)
	  if(a[i]!=b[i]) id[++num]=i;
	id[++num]=1e9;
	for(int i=1;i<num;++i)
	  if(id[i+1]-id[i]>=2||a[id[i+1]]==a[id[i]]) ++ans;
	  else ans+=id[i+1]-id[i],++i;
	return printf("%d",ans),0;
}
```
### [D　　Valid BFS?](http://codeforces.com/contest/1037/problem/D)
#### 题目大意
给定一棵从$1$开始编号的树，要求$bfs$这棵树，每次$bfs$当前点都找没遍历过的随意一个相邻点加入队列，遍历完了就从队列头取出一个点重新遍历

现在给你一个长度为$n$的序列，里面是$bfs$遍历顺序，先遍历的点先出现，问这个$bfs$序列可不可能出现

#### 题解
终于不是贪心啦！~~然而是模拟~~

先把树建出来，然后记录下来每个点的父亲，深度和儿子个数

因为每个深度的点肯定是一段连续的区间，处理这个点的时候，看下一深度区间里对应的点的父亲是否是ta

有些小细节，注意一下就行了

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
const int MAX=2e5+1;
struct p{
	int x,y;
}c[MAX<<1];
int n,num,maxn;
int h[MAX],d[MAX],id[MAX],siz[MAX],fa[MAX],W[MAX];
void add(int x,int y)
{
	c[++num]=(p){h[x],y},h[x]=num;
	c[++num]=(p){h[y],x},h[y]=num;
}
void dfs(int x,int f)
{
	++W[f],fa[x]=f,d[x]=d[f]+1,maxn=max(maxn,d[x]),++siz[d[x]];
	for(int i=h[x];i;i=c[i].x)
	  if(c[i].y!=f) dfs(c[i].y,x);
}
int main()
{
	scanf("%d",&n);
	for(int i=1,x,y;i<n;++i)
	  scanf("%d%d",&x,&y),add(x,y);
	for(int i=1;i<=n;++i)
	  scanf("%d",&id[i]);
	dfs(1,0);
	int L=1,Siz=siz[L];
	for(int i=1;i<=n;++i)
	  {
	  	if(!Siz) Siz=siz[++L];
	  	if(d[id[i]]!=L) return printf("No"),0;
	  	--Siz;
	  }
	for(int i=1;i<=maxn;++i)
	  siz[i]+=siz[i-1];
	for(int i=1;i<=n;++i)
	  {
	  	int Id=id[i];
	  	if(d[Id]!=d[id[i-1]]) L=siz[d[Id]]+1;
	  	for(int j=L;j<=L+W[Id]-1;++j)
	  	  if(fa[id[j]]!=Id) return printf("No"),0;
	  	L+=W[Id];
	  }
	return printf("Yes"),0;
}
```
### [E　　Trips](http://codeforces.com/contest/1037/problem/E)
#### 题目大意
一共有$n$个人，他们开始互不认识，而每天早上不认识的两个人会变成朋友

一共有$m$天，每天晚上有的人要去旅行，去旅行的人必须满足ta有至少$k$个朋友也去旅行

求每天去旅行的最大人数

#### 题解
正着建边好像不可做啊...换个思路？

可以开始先把所有的边建起来，然后依次拆边

这样如果一个点开始就不满足条件ta就永远不满足条件

如果一个点的度不小于$k$就先不管ta

如果一个点的度小于$k$，说明这个点不满足条件，可以删掉ta，删掉ta的同时与ta相连的点度也要$-1$

这样可能出现连锁反应，用个队列记录一下，复杂度差不多$O(n+m)$

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<vector>
# include<queue> 
# include<algorithm>
using namespace std;
const int MAX=2e5+1;
int n,m,k,ans;
int du[MAX],X[MAX],Y[MAX],Ans[MAX];
vector<int> ve[MAX];
bool use[MAX];
void work(int x)
{
	queue<int> qu;
	qu.push(x),use[x]=1,--ans;
	while(!qu.empty())
	{
		int tt=qu.front();
		qu.pop();
		for(int i=0;i<ve[tt].size();++i)
		  {
		  	int y=ve[tt][i];
			if(use[y]) continue;
			--du[y];
			if(du[y]<k) --ans,use[y]=1,qu.push(y);
		  }
	}
}
int main()
{
	scanf("%d%d%d",&n,&m,&k);
	ans=n;
	for(int i=1;i<=m;++i)
	  {
	  	scanf("%d%d",&X[i],&Y[i]);
	  	++du[X[i]],++du[Y[i]],ve[X[i]].push_back(Y[i]),ve[Y[i]].push_back(X[i]);
	  }
	for(int i=1;i<=n;++i)
	  if(du[i]<k&&!use[i]) work(i);
	Ans[m]=ans;
	for(int i=m;i>=1;--i)
	  {
	  	if(!use[Y[i]]) --du[X[i]];
		if(!use[X[i]]) --du[Y[i]];
	  	ve[X[i]].pop_back(),ve[Y[i]].pop_back();
	  	if(du[X[i]]<k&&!use[X[i]]) work(X[i]);
	  	if(du[Y[i]]<k&&!use[Y[i]]) work(Y[i]);
	  	Ans[i-1]=ans;
	  }
	for(int i=1;i<=m;++i)
	  printf("%d\n",Ans[i]);
	return 0;
}
```
### [H　　Security](https://codeforces.com/problemset/problem/1037/H)
#### [题目大意+题解+代码](https://a-failure.github.io/2019/01/09/CF1037H-Security/)
