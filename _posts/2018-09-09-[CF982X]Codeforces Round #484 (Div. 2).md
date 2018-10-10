---
layout:     post
title:      "[CF982X]Codeforces Round #484 (Div. 2)"
date:       2018-09-09
author:     "Dispwnl"
header-img: "img/used/352.jpg"
catalog: true
tags:
    - 比赛
---
>掉的最惨的一场终于全A了QAQ

### [A　　Row](http://codeforces.com/contest/982/problem/A)
#### 题目大意
给你一个$01$序列，要求满足以下条件：

- $1$的相邻位置没有$1$
- 在满足条件一的情况下，没有$0$可以替换成$1$

问这个序列是否满足条件

#### 题解
虽然题面很简单但是要考虑的细节很多...尤其是$001,0100,0$之类的情况~~当时WA了7发~~

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
int n,l,p=-2;
string a;
int main()
{
	scanf("%d",&n);
	cin>>a;
	l=a.length();
	for(int i=0;i<l;++i)
	  if(a[i]=='1')
	  {
	  	if(i-p==1||i-p>3) return printf("No"),0;
	  	else p=i;
	  }
	if(l-p-1>=2||p==-1) return printf("No"),0;
	return printf("Yes"),0;
}
```
### [B　　Bus of Characters](http://codeforces.com/contest/982/problem/B)
#### 题目大意
有$n$行，每行两个座位，每行的座位有不同的宽度

有两种人，第一种会选择没有人的一行，如果有多种选择，ta会选一个座位宽度最小的一行；第二种会选择有一个人的一行，如果有多种选择，ta会选择座位宽度最大的一行

给定入座人的顺序，问每个人坐在第几行

#### 题解
排下序，因为第一种人座位是递增的，所以拿个栈维护一下就好，因为数组开小WA了一发QAQ

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
# include<stack>
using namespace std;
const int MAX=4e5+1;
struct p{
	int w,id;
	bool operator<(const p&a)
	const{
		return w<a.w;
	}
}s[MAX];
int n;
int ans[MAX];
stack<int> st;
int read()
{
	int x=0;
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
int main()
{
	n=read();
	for(int i=1;i<=n;++i)
	  s[i].w=read(),s[i].id=i;
	sort(s+1,s+1+n);
	int l=1;
	string a;
	cin>>a;
	for(int i=0;i<2*n;++i)
	  {
	  	if(a[i]=='0') ans[i+1]=s[l].id,st.push(s[l].id),++l;
	  	else ans[i+1]=st.top(),st.pop();
	  }
	for(int i=1;i<=2*n;++i)
	  printf("%d ",ans[i]);
	return 0;
}
```
### [C　　Cut 'em all!](http://codeforces.com/contest/982/problem/C)
#### 题目大意
给定一棵树，问在保证每个连通块点数为偶数的情况下最多能切多少条边

#### 题解
>$Google$翻译把$even$翻译成了“均匀的”
>![](https://a-failure.github.io/img/3423.jpg)

首先点数为奇数肯定不合法...

因为偶数-偶数还是偶数，所以只要子树大小为偶数，就可以从ta的父亲处分离，简单贪心即可

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
const int MAX=1e5+1;
struct p{
	int x,y;
}c[MAX<<1];
int n,num,ans;
int h[MAX],siz[MAX];
void add(int x,int y)
{
	c[++num]=(p){h[x],y},h[x]=num;
	c[++num]=(p){h[y],x},h[y]=num;
}
void dfs(int x,int f=0)
{
	siz[x]=1;
	for(int i=h[x];i;i=c[i].x)
	  if(c[i].y!=f) dfs(c[i].y,x),siz[x]+=siz[c[i].y];
	if(siz[x]%2==0&&f) ++ans;
}
int main()
{
	scanf("%d",&n);
	if(n%2) return printf("-1"),0;
	for(int i=1,x,y;i<n;++i)
	  scanf("%d%d",&x,&y),add(x,y);
	dfs(1);
	return printf("%d",ans),0;
}
```
### [D　　Shark](http://codeforces.com/contest/982/problem/D)
#### 题目大意
给定一个序列，要求你找到一个$k$，以值不小于$k$的位置为分界点，满足以下条件：

- 分出来的每一段长度都相等
- 满足条件一的情况下段数最多
- 满足条件一、二的情况下$k$值最小

#### 题解
可以发现最优的$k$的值肯定是序列的某个值$+1$，而且$k$值变大时，段的长度是单调不减的

考虑从小到大枚举序列，每次影响的肯定是这个位置左右两边的区间，用并查集维护一下，看左右的值是否小于$k$

合并区间时顺带维护长度，所有区间都是一个长度时就可以更新答案了

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<set>
# include<algorithm>
# define LL long long
using namespace std;
const int MAX=1e5+5;
struct p{
	int x,id;
	bool operator< (const p &a)
	const{
		return x<a.x;
	}
}a[MAX];
int n,ans,K;
int fa[MAX],siz[MAX],tot[MAX],b[MAX];
set<int> S;
int find(int x)
{
	if(fa[x]!=x) fa[x]=find(fa[x]);
	return fa[x];
}
int main()
{
	scanf("%d",&n);
	for(int i=1;i<=n;++i)
	  scanf("%d",&a[i].x),b[i]=a[i].x,a[i].id=i,fa[i]=i,siz[i]=1;
	sort(a+1,a+1+n);
	b[0]=b[n+1]=2e9;
	for(int i=1;i<=n;++i)
	  {
		int id=a[i].id,k=a[i].x+1;
		S.insert(1),++tot[1];
	  	if(b[id-1]<k)
	  	{
	  		int r1=find(id-1);
	  		--tot[1],--tot[siz[r1]];
	  		if(!tot[1]) S.erase(1);
	  		if(!tot[siz[r1]]) S.erase(siz[r1]);
			fa[id]=r1,++tot[++siz[r1]];
			if(tot[siz[r1]]==1) S.insert(siz[r1]);
		}
		if(b[id+1]<k)
		{
			int r1=find(id+1),r2=find(id);
			--tot[siz[r1]],--tot[siz[r2]];
			if(!tot[siz[r1]]) S.erase(siz[r1]);
			if(!tot[siz[r2]]) S.erase(siz[r2]);
			fa[r1]=r2,++tot[siz[r2]+=siz[r1]];
			if(tot[siz[r2]]==1) S.insert(siz[r2]);
		}
		if(S.size()==1&&tot[*S.begin()]>ans) ans=tot[*S.begin()],K=k;
	  }
	return printf("%d",K),0;
}
```
### [E　　Billiard](http://codeforces.com/contest/982/problem/E)
#### 题目大意
从某个点打出一个台球，发射角度只能是$45°$角和ta的倍数，除非进入口袋，否则台球不会停下来
![](http://codeforces.com/predownloaded/99/14/99142760998168e77abc4cc20f27586204051783.png)
问台球是否会停下来，如果会，输出进入的是哪个口袋

#### 题解
> 原来用暴力PP然后成功被人$hack$了QAQ

假设台球在$(2,3)$，$n=5,m=4$，方向向右上
![](https://a-failure.github.io/img/study/billiard1.png)
由于对称，可以化成这样
![](https://a-failure.github.io/img/study/billiard2.png)
延长一下
![](https://a-failure.github.io/img/study/billiard3.png)
所以题目要求$(y-x)+am=bn$，即$am+bn=(y-x)$

其他方向也是一样，可以全部转换成右上的情况，最后答案再取反

$exgcd$解决即可，注意不合法情况的判断

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
# define LL long long
using namespace std;
int n,m,X,Y,vx,vy,ans1,ans2;
int exgcd(int a,int b,LL &x,LL &y)
{
	if(!b) return x=1,y=0,a;
	int ans=exgcd(b,a%b,x,y),tt=x;
	return x=y,y=tt-a/b*y,ans;
}
int main()
{
	scanf("%d%d%d%d%d%d",&n,&m,&X,&Y,&vx,&vy),ans1=n,ans2=m;
	if(X&&X!=n&&!vx) return printf("-1"),0;
	if(Y&&Y!=m&&!vy) return printf("-1"),0;
	if((!X||X==n)&&!vx)
	{
		if(vy>0) return printf("%d %d",X,m),0;
		if(vy<0) return printf("%d 0",X),0;
	}
	if((!Y||Y==m)&&!vy)
	{
		if(vx>0) return printf("%d %d",n,Y),0;
		if(vx<0) return printf("0 %d",Y),0;
	}
	if(vx==-1) X=n-X,ans1=0;
	if(vy==-1) Y=m-Y,ans2=0;
	LL x,y,gcd=exgcd(n,m,x,y);
	if((X-Y)%gcd) return printf("-1"),0;
	LL tt=(X-Y)/gcd,M=m/gcd;
	x*=tt,y*=tt,x=(x%M+M-1)%M+1,y=(X-Y-x*n)/m;
	if(x%2==0) ans1=n-ans1;
	if(y%2==0) ans2=m-ans2;
	return printf("%d %d",ans1,ans2),0;
}
```

### [F　　The Meeting Place Cannot Be Changed](http://codeforces.com/contest/982/problem/F)
#### 题目大意
给定一个有向图，有一个不断运动的点，你不知道这个点起点在哪，求无论起点在哪、运动路径如何运动点一定经过的点，并且这个点入度出度均不为$0$

#### 题解
首先没有解的情况：

- 图中没有环
- 去掉一个环剩下的图中还有环

对于一个环，如图：
![](https://a-failure.github.io/img/study/tmpcbc1.png)
显然图中红色部分是必须要经过的

所以先找图中的环，然后找入边和出边，把中间的点打上标记

最后求个前缀和，标记数为满足条件的弧的数量就是答案了

#### 代码
```
# include<bits/stdc++.h>
using namespace std;
const int MAX=1e5+5;
struct p{
	int x,y;
}c[MAX*5];
int n,m,num,ans,top,cnt,tot,Ans,ANS;
int pos[MAX],pre[MAX],vis[MAX],sum[MAX],ww[MAX],w[MAX],h[MAX],st[MAX],dfn[MAX],low[MAX],siz[MAX];
bool use[MAX],Use[MAX];
void add(int x,int y)
{
	c[++num]=(p){h[x],y},h[x]=num;
}
void dfs(int x)
{
	if(tot||x==Ans) return;
	vis[x]=-1;
	for(int i=h[x];i&&!tot;i=c[i].x)
	  if(c[i].y!=Ans)
	  {
	  	if(vis[c[i].y]==-1)
		{
	  		int tt=x;
	  		while(tt!=c[i].y) ww[++tot]=tt,tt=pre[tt];
	  		return (void)(ww[++tot]=c[i].y);
	  	}
	  	else if(!vis[c[i].y]) pre[c[i].y]=x,dfs(c[i].y);
	  }
	vis[x]=1;
}
void dfs1(int x,int f)
{
	if(x==Ans) return;
	use[x]=1;
	for(int i=h[x];i;i=c[i].x)
	  {
	  	int y=c[i].y;
	  	if(y==Ans) continue;
	  	if(!Use[y])
		{
			if(!use[y]) dfs1(y,f);
		}
	  	else if(!Use[x]&&pos[f]<pos[y])
	  	++ANS,++sum[1],--sum[pos[f]+1],++sum[pos[y]],--sum[tot+1];
	  }
}
void dfs2(int x,int f)
{
	if(x==Ans) return;
	use[x]=1;
	for(int i=h[x];i;i=c[i].x)
	  {
	  	int y=c[i].y;
	  	if(y==Ans) continue;
	  	if(!Use[y])
		{
			if(!use[y]) dfs2(y,f);
		}
	  	else if(!Use[x]&&pos[f]>=pos[y])
	  	++ANS,--sum[pos[f]+1],++sum[pos[y]];
	  }
}
bool solve()
{
	tot=0,ANS=0;
	memset(vis,0,sizeof(vis));
	memset(sum,0,sizeof(sum));
	for(int i=1;i<=n;++i)
	  if(!tot) dfs(i);
	  else break;
	memset(Use,0,sizeof(Use));
	memset(pos,0,sizeof(pos));
	for(int i=1;i<=tot;++i)
	  w[i]=ww[tot-i+1],Use[w[i]]=1,pos[w[i]]=i;
	memset(use,0,sizeof(use));
	for(int i=1;i<=tot;++i)
	  dfs1(w[i],w[i]);
	memset(use,0,sizeof(use));
	for(int i=tot;i>=1;--i)
	  dfs2(w[i],w[i]);
	for(int i=1;i<=tot;++i)
	  if((sum[i]+=sum[i-1])==ANS) return Ans=w[i];
	return 0;
}
int main()
{
	scanf("%d%d",&n,&m);
	for(int i=1,x,y;i<=m;++i)
	  scanf("%d%d",&x,&y),add(x,y);
	if(!solve()) return printf("-1"),0;
	if(solve()) return printf("-1"),0;
	return printf("%d",Ans),0;
}
```
