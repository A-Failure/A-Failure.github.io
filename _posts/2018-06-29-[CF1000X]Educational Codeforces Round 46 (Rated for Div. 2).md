---
layout:     post
title:      "[CF1000X]Educational Codeforces Round 46 (Rated for Div. 2)"
date:       2018-06-29
author:     "Dispwnl"
header-img: "img/used/007.jpg"
catalog: true
tags:
    - 比赛
---
>我怎么这么菜啊QAQ

### [A　　Codehorses T-shirts](http://codeforces.com/contest/1000/problem/A)
#### 题目大意
$n$个字符串和$n$个模板串，字符串只有可能是<code>M,L,S,XL,XXL,XXXL,XS,XXS,XXXS</code>，在$1s$的时间内你可以选择一个字符串（不是模板串）修改任意多的字符，但是不能删除或增加字符，问至少需要多少$s$使得字符串变为模板串，注意不考虑串的顺序，即最后每个字符串出现次数=这个串在模板串中的出现次数

#### 题解
开始似乎看错了题意然后码了个网络流~~A题怎么可能~~...？

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<queue>
# include<algorithm>
using namespace std;
struct p{
    int x,y,dis,cn;
}c[100001];
int n,m,s,t=5000,num,tot,tot1;
int h[5001],d1[5001],pre[5001];
bool use[5001],use1[5001];
int D[101][101];
string a[101],b[101],aa[101],bb[101];
int main()
{
    cin>>n;
	memset(D,1,sizeof(D));
	for(int i=1;i<=n;++i)
	  cin>>a[i];
	sort(a+1,a+1+n);
	for(int i=1;i<=n;++i)
	  cin>>b[i];
	sort(b+1,b+1+n);
	for(int i=1;i<=n;++i)
	  for(int j=1;j<=n;++j)
	    if(a[i]==b[j]&&!use[i]&&!use1[j]) use[i]=use1[j]=1;
	for(int i=n;i>=1;--i)
	  {
	  	if(!use[i]) aa[++tot]=a[i];
	  	if(!use1[i]) bb[++tot1]=b[i];
	  }
	int ans=0;
	for(int i=1;i<=tot;++i)
	  if(aa[i]!=bb[i]) ++ans;
    printf("%d",ans);
    return 0;
}
```
### [B　　Light It Up](http://codeforces.com/contest/1000/problem/B)
#### 题目大意
有一台灯，这个灯在时间为$0$时打开，$m$时关闭，在$0$到$m$这段时间内有$n$个时间点灯的状态会改变（即开变关，关变开），现在可以在剩余的时间点选一个让灯的状态改变一次，求这个灯最大亮着的时间

#### 题解
贪心，最优情况肯定是在某个$a$后面相邻的位置加，枚举就行了

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<cmath>
# include<algorithm>
using namespace std;
int n,m;
int a[100004];
int l[100004],sum[2][100004];
int main()
{
	cin>>n>>m;
	for(int i=1;i<=n;++i)
	  cin>>a[i];
	a[++n]=m;
	for(int i=1;i<=n;++i)
	  l[i]=a[i]-a[i-1],sum[!(i&1)][i]=sum[!(i&1)][i-1],sum[i&1][i]=sum[i&1][i-1]+l[i];
	int maxn=sum[1][n];
	for(int i=1;i<n;++i)
	  if(a[i+1]!=a[i]+1&&(i&1))
	  maxn=max(maxn,sum[1][i]+sum[0][n]-sum[0][i+1]+l[i+1]-1);
	printf("%d",maxn);
}
```
### [C　　Covered Points Count](http://codeforces.com/contest/1000/problem/C)
#### 题目大意
给你$n$个区间，求被这些区间覆盖层数为$k(k\leq n)$的点的个数

#### 题解
离散化加前缀和...$fuge$搞了个树状数组跑的还贼快Orz

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
# define LL long long
using namespace std;
const int MAX=5e5+1;
LL t,n,cnt;
LL ans[MAX],X[MAX],Y[MAX],a[MAX],sum[MAX],dis[MAX];
LL IIDD(LL x)
{
	LL t=lower_bound(a+1,a+n+1,x)-a;
	return t;
}
int main()
{
	cin>>t;
	for(int i=1;i<=t;++i)
	  cin>>X[i]>>Y[i],a[++cnt]=X[i],a[++cnt]=Y[i];
	sort(a+1,a+cnt+1);
	n=unique(a+1,a+cnt+1)-a-1;
	for(int i=1;i<=t;++i)
	  {
		LL xx=IIDD(X[i]),yy=IIDD(Y[i]);
		++dis[yy],++sum[xx],--sum[yy+1];
	  }
	for(int i=1;i<=n;++i)
		sum[i]=sum[i]+sum[i-1];
	for(int i=1; i<=n-1; ++i)
	  ++ans[sum[i]],ans[sum[i]-dis[i]]+=a[i+1]-a[i]-1;
	++ans[sum[n]];
	for(int i=1;i<=t;++i)
	  cout<<ans[i]<<" ";
	return 0;
}
```
### [D　　Yet Another Problem On a Subsequence](http://codeforces.com/contest/1000/problem/D)
#### 题目大意
如果一个数组$[a_1,a_2,a_3,...,a_n]a_1=n−1$并且$a1>0$，这个数组就被叫为<code>好数组</code>，如果一个序列能正好分为多个<code>好数组</code>，ta就被叫为<code>好序列</code>，现在给定一个序列，求这个序列有多少子序列是<code>好序列</code>，答案对$998244353$取模

#### 题解
用$f_i$来维护从$i$向后最多能有多少答案

$C$表示组合数，先初始化$f_i=C(n-i,a_i)$

$n^2$的转移，从后往前枚举$i$，然后枚举$i+a_i+1$~$n$

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
# define LL long long
using namespace std;
const int MAX=1e3+1,mod=998244353;
int n;
int a[MAX];
LL f[MAX];
int c[MAX][MAX];
void work()
{
	for(int i=0;i<=n;++i)
	  c[i][0]=1;
	for(int i=1;i<=n;++i)
	  for(int j=1;j<=i;++j)
	    c[i][j]=(c[i-1][j]+c[i-1][j-1])%mod;
}
int main()
{
	scanf("%d",&n);
	work();
	for(int i=1;i<=n;++i)
	  {
	  	scanf("%d",&a[i]);
	  	if(a[i]>0&&a[i]<=n) f[i]=c[n-i][a[i]];
	  }
	LL ans=0;
	for(int i=n;i>=1;--i)
	  {
	  	if(a[i]<=0||a[i]>=n) continue;
	  	for(int j=i+a[i]+1;j<=n;++j)
	  	  (f[i]+=(1ll*f[j]*c[j-i-1][a[i]]))%=mod;
	  	(ans+=f[i])%=mod;
	  }
	printf("%d",ans);
	return 0;
}
```
### [E　　We Need More Bosses](http://codeforces.com/contest/1000/problem/E)
#### 题目大意

给定一个$n$个点$m$条边的无向图，找到两个点$s,t$，使得$s$到$t$必须经过的边最多（一条边无论走哪条路线都经过ta，这条边就是必须经过的边），$2\leq n\leq 3\times 10^5,1\leq m\leq 3\times 10^5$

#### 题解
题意理解就很明了了，找到无向图缩点后的直径

我现在才知道双点缩点和双边缩点$Tarjan$写法不一样~~虽然就加一句~~。。。

缩点后图变成了树，求树的直径就行了

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<stack>
# include<queue>
# include<algorithm>
using namespace std;
const int MAX=3e5+1;
struct p{
	int x,y;
}c[MAX<<1],cc[MAX<<1],ccc[MAX<<1];
int n,m,num=1,num1,cnt,ans,maxn,ss;
int h[MAX],hh[MAX],col[MAX],dfn[MAX],low[MAX],d[MAX];
bool use[MAX];
stack<int> st;
void add(int x,int y)
{
	c[++num]=(p){h[x],y},h[x]=num;
	c[++num]=(p){h[y],x},h[y]=num;
}
void Add(int x,int y)
{
	ccc[++num1]=(p){hh[x],y},hh[x]=num1;
	ccc[++num1]=(p){hh[y],x},hh[y]=num1;
}
void tarjan(int x,int id)
{
	dfn[x]=low[x]=++cnt,use[x]=1;
	st.push(x);
	for(int i=h[x];i;i=c[i].x)
	  if(!dfn[c[i].y]) tarjan(c[i].y,i),low[x]=min(low[x],low[c[i].y]);
	  else if(i!=(id^1)) low[x]=min(low[x],dfn[c[i].y]);
	if(low[x]==dfn[x])
	{
		int tt=-1;
		++ans;
		while(tt!=x)
		tt=st.top(),st.pop(),use[tt]=0,col[tt]=ans;
	}
}
void dfs(int x,int f)
{
	if(maxn<(d[x]=d[f]+1)) maxn=d[x],ss=x;
	for(int i=hh[x];i;i=ccc[i].x)
	  if(ccc[i].y!=f) dfs(ccc[i].y,x);
}
int work()
{
	int s=1,Max=0;
	while(1)
	{
		memset(d,0,sizeof(d));
		maxn=0,ss=0;
		dfs(s,0);
		if(maxn<=Max) return Max-1;
		Max=maxn,s=ss;
	}
}
int main()
{
	scanf("%d%d",&n,&m);
	for(int i=1;i<=m;++i)
	  {
	  	scanf("%d%d",&cc[i].x,&cc[i].y);
	  	add(cc[i].x,cc[i].y);
	  }
	for(int i=1;i<=n;++i)
	  if(!dfn[i]) tarjan(i,0);
	for(int i=1;i<=m;++i)
	  if(col[cc[i].x]!=col[cc[i].y]) Add(col[cc[i].x],col[cc[i].y]);
	printf("%d",work());
	return 0;
}
```
### [F　　One Occurrence](http://codeforces.com/contest/1000/problem/F)
#### 题目大意
给定一个长度为$n$序列，$m$个询问，每次询问给定一个区间$[l,r]$，如果这个区间里存在只出现一次的数，输出这个数（如果有多个就输出任意一个），没有就输出0，$n,m\leq 5\times 10^5$

#### 题解
> 当时觉得这题好难啊……现在看就是线段树/莫队sb题……

正解是线段树，每个节点存一个$pair$，维护这个点能产生贡献的区间最左端点和这个点代表的数

用$pre_{a_i}$表示数$a_i$左边出现$a_i$（即上一个）的位置

先把询问处理一下，位置$r$存下有哪些询问以ta做右端点

然后从左到右扫一遍序列，如果$pre_{a_i}$不为$0$，就把线段树中位置为$pre_{a_i}$的点清空

因为现在的$i$是“上一个”的位置了，如果$pre_{a_i}$能给答案产生贡献应该在$i$前

然后$i$位置存下$pre_{a_i}+1$和$a_i​$

如果$i$是某个询问的右端点，线段树查询区间$[l,i]$里有没有答案

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<vector>
# include<algorithm>
# define tl (k<<1)
# define tr (k<<1|1)
# define mid (l+r>>1)
# define mp make_pair
# define X first
# define Y second
# define P pair<int,int>
using namespace std;
const int MAX=5e5+1;
int n,m;
int ans[MAX],l[MAX],v[MAX],pr[MAX];
pair<int,int> s[MAX<<2];
vector<int> a[MAX];
P Min(P a,P b)
{
	if(!a.first) return b;
	if(!b.first) return a;
	return min(a,b);
}
void pus(int k) {s[k]=Min(s[tl],s[tr]);}
void change(int l,int r,int k,int x,int a,int b)
{
	if(l==r) return void(s[k]=mp(a,b));
	if(x<=mid) change(l,mid,tl,x,a,b);
	else change(mid+1,r,tr,x,a,b);
	pus(k);
}
P ask(int l,int r,int k,int L,int R)
{
	if(l==L&&r==R) return s[k];
	if(R<=mid) return ask(l,mid,tl,L,R);
	if(L>mid) return ask(mid+1,r,tr,L,R);
	return Min(ask(l,mid,tl,L,mid),ask(mid+1,r,tr,mid+1,R));
}
int read()
{
	int x(0);
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
int main()
{
	n=read();
	for(int i=1;i<=n;++i)
	  v[i]=read();
	m=read();
	for(int i=1,r;i<=m;++i)
	  l[i]=read(),r=read(),a[r].push_back(i);
	for(int i=1;i<=n;++i)
	  {
	  	if(pr[v[i]]) change(1,n,1,pr[v[i]],0,0);
	  	change(1,n,1,i,pr[v[i]]+1,v[i]),pr[v[i]]=i;
	  	for(int j=0;j<a[i].size();++j)
	  	  {
	  	  	P Ans=ask(1,n,1,l[a[i][j]],i);
	  	  	if(Ans.X<=l[a[i][j]]) ans[a[i][j]]=Ans.Y;
		  }
	  }
	for(int i=1;i<=m;++i)
	  printf("%d\n",ans[i]);
	return 0;
}
```
