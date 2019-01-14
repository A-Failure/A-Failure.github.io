---

layout:		post

title:		"[CF1100X]Codeforces Round #532 (Div. 2)"

date:		2019-01-14

author:		"Dispwnl"

header-img:	"img/used/45697.jpg"

catalog:	false

tags:
    - 比赛
---

### [A　　Roman and Browser](https://codeforces.com/contest/1100/problem/A)

#### 题目大意

给定一个长度为$n​$的只有$1​$和$-1​$的序列，选择一个位置$b​$，然后删掉位置为$b+i\times k​$的数（$i​$为整数），求操作后$1​$和$-1​$数量的最大绝对差值

#### 题解

。

#### 代码

```c++
# include<bits/stdc++.h>
using namespace std;
const int MAX=1e3+4; 
int a[MAX];
int main()
{
	int n,k;
	scanf("%d%d",&n,&k);
	int Sum=0,Sum1=0;
	for(int i=1;i<=n;++i)
	  {
	  	scanf("%d",&a[i]);
	  	if(a[i]==1) ++Sum;
	  	else ++Sum1;
	  }
	int ans=0;
	for(int i=1;i<=n;++i)
	  {
	  	int sum=0,sum1=0;
	  	for(int j=(i%k?i%k:k);j<=n;j+=k)
	  	  if(a[j]==1) ++sum;
	  	  else ++sum1;
	  	ans=max(ans,abs(Sum-sum-Sum1+sum1));
	  }
	return printf("%d",ans),0;
}
```

### [B　　Build a Contest](https://codeforces.com/contest/1100/problem/B)

#### 题目大意

有$n​$个题，每个题有一个难度$a_i(1\le a_i\le m)​$，从左往右加入题，当加入的题中$m​$个难度都出现时，输出$1​$并把每个难度都删除一道题，否则输出$0​$，求输出序列

#### 题解

因为每个题最多加入/删除一次，所以维护有当前有多少种难度，$m​$个的时候暴力判断修改就行了

#### 代码

```c++
# include<bits/stdc++.h>
using namespace std;
const int MAX=1e5+5;
int num[MAX];
int a[MAX];
int main()
{
	int n,m;
	scanf("%d%d",&n,&m);
	for(int i=1;i<=m;++i)
	  scanf("%d",&a[i]);
	for(int i=1,sum=0;i<=m;++i)
	  {
	  	if(!num[a[i]]) ++sum;
	  	++num[a[i]];
	  	if(sum==n)
	  	{
	  		printf("1"),sum=0;
	  		for(int j=1;j<=n;++j)
	  		  {
	  		  	--num[j];
	  		  	if(num[j]) ++sum;
			  }
		}
		else printf("0");
	  }
	return 0;
}
```

### [C　　NN and the Optical Illusion](https://codeforces.com/contest/1100/problem/C)

#### 题目大意

有$n$个外圆围着一个半径为$r$的内圆，正好把内圆包起来，求外圆半径

#### 题解

画个图就知道了

#### 代码

```c++
# include<bits/stdc++.h>
using namespace std;
const double Pi=acos(-1.0);
double n,r;
int main()
{
	scanf("%lf%lf",&n,&r);
	double A=Pi/n,R=r/(1/sin(A)-1);
	return printf("%.10lf",R),0;
}
```

### [D　　Dasha and Chess](https://codeforces.com/contest/1100/problem/D)

#### 题目大意

交互题

$999 \times 999$的棋盘上，有一个白王和$666$个黑车，每次白王可以往$8$个方向走一格，黑车可以随便移动到某个没有棋子的位置，当黑车移动后白王和某个黑车在同一行/列时白王赢，求白王在$2000$步之内的必胜策略

#### 题解

如果白王移动到$(500,500)$的话，会把棋盘分为$4$块

最劣情况下，任意$3$块棋盘的和的最大值为$166+167+167=500$

这样移动白王向最大值的方向移动，相当于横纵坐标都扫一遍，最劣情况下也会有至少一行/列黑车的数量$>=2$，所以这样存在必胜策略

#### 代码

```c++
# include<bits/stdc++.h>
using namespace std;
const int MAX=1e3+5;
struct p{
	int x,y;
}s[MAX];
int X,Y;
bool use[MAX][MAX];
void ask(int _x,int _y)
{
	int k,x,y;
	if(use[X+_x][Y+_y]) _y=0;
	printf("%d %d\n",X+=_x,Y+=_y);
	fflush(stdout);
	scanf("%d%d%d",&k,&x,&y);
	if(k==-1) exit(0);
	if(!k) exit(0);
	use[s[k].x][s[k].y]=0,use[s[k].x=x][s[k].y=y]=1;
}
int main()
{
	scanf("%d%d",&X,&Y);
	for(int i=1;i<=666;++i)
	  scanf("%d%d",&s[i].x,&s[i].y),use[s[i].x][s[i].y]=1;
	while(X<500) ask(1,0);
	while(X>500) ask(-1,0);
	while(Y<500) ask(0,1);
	while(Y>500) ask(0,-1);
	int cnt1=0,cnt2=0,cnt3=0,cnt4=0;
	for(int i=1;i<=999;++i)
	  for(int j=1;j<=999;++j)
	    if(use[i][j])
		{
			if(i<500||j<500) ++cnt1;
			if(i<500||j>500) ++cnt2;
			if(i>500||j<500) ++cnt3;
			if(i>500||j>500) ++cnt4;
		}
	if(cnt1>=500) while(1) ask(-1,-1);
	if(cnt2>=500) while(1) ask(-1,1);
	if(cnt3>=500) while(1) ask(1,-1);
	if(cnt4>=500) while(1) ask(1,1);
	return 0;
}
```



### [E　　Andrew and Taxi](https://codeforces.com/contest/1100/problem/E)

#### 题目大意

给定一个带权有向图，可以改变一些边的方向使得图中没有环，最小化改变的边权值的最大值并输出方案

#### 题解

因为要使最大权值最小，可以想到二分答案一个$mid$，因为权值不大于$mid$的边可以随便改动，所以只用判断权值大于$mid$的边是否组成环即可

输出方案就把拓扑序小的往拓扑序大的上面连即可

#### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
const int MAX=1e5+5;
struct p{
	int x,y,dis;
}c[MAX],cc[MAX];
int n,m,num,cnt,top,tot,ans;
int h[MAX],low[MAX],dfn[MAX],st[MAX],Ans[MAX],du[MAX],qu[MAX],id[MAX];
bool use[MAX];
void add(int x,int y) {c[++num]=(p){h[x],y},h[x]=num;}
void Tarjan(int x)
{
	use[x]=1,st[++top]=x,low[x]=dfn[x]=++cnt;
	for(int i=h[x];i;i=c[i].x)
	  if(!dfn[c[i].y]) Tarjan(c[i].y),low[x]=min(low[x],low[c[i].y]);
	  else if(use[c[i].y]) low[x]=min(low[x],dfn[c[i].y]);
	if(low[x]==dfn[x])
	{
		++ans;
		int tt=-1;
		while(tt!=x) tt=st[top--],use[tt]=0;
	}
}
bool look(int mid)
{
	memset(h,0,sizeof(h));
	memset(low,0,sizeof(low));
	memset(dfn,0,sizeof(dfn));
	memset(use,0,sizeof(use));
	top=num=cnt=ans=0;
	for(int i=1;i<=m;++i)
	  if(cc[i].dis>mid) add(cc[i].x,cc[i].y);
	for(int i=1;i<=n;++i)
	  if(!dfn[i]) Tarjan(i);
	return ans==n;
}
void work(int ans)
{
	memset(h,0,sizeof(h));
	num=0;
	int hl=1,tl=num=0;
	for(int i=1;i<=m;++i)
	  if(cc[i].dis>ans) add(cc[i].x,cc[i].y),++du[cc[i].y];
	for(int i=1;i<=n;++i)
	  if(!du[i]) qu[++tl]=i;
	while(hl<=tl)
	{
		int tt=qu[hl++];
		id[tt]=hl;
		for(int i=h[tt];i;i=c[i].x)
		  if(!--du[c[i].y]) qu[++tl]=c[i].y;
	}
	for(int i=1;i<=m;++i)
	  if(cc[i].dis<=ans&&id[cc[i].x]>id[cc[i].y]) Ans[++tot]=i;
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
	n=read(),m=read();
	int l=1e9,r=0,ans;
	for(int i=1;i<=m;++i)
	  cc[i].x=read(),cc[i].y=read(),cc[i].dis=read(),l=min(l,cc[i].dis),r=max(r,cc[i].dis);
	--l;
	while(l<=r)
	{
		int mid(l+r>>1);
		if(look(mid)) ans=mid,r=mid-1;
		else l=mid+1;
	}
	work(ans);
	if(!tot) ans=0;
	printf("%d %d\n",ans,tot);
	for(int i=1;i<=tot;++i)
	  printf("%d ",Ans[i]);
	return 0;
}
```

### [F　　Ivan and Burgers](https://codeforces.com/contest/1100/problem/F)

#### 题目大意

给定一个长度为$n$的序列，每次询问查询一个区间$[l,r]$，求$[l,r]$中最大异或和是多少

#### 题解

刚看这题：线段树+线性基合并！

$n$是$5e5$……

考虑离线做法，线性基每一位记录最靠右的位置，然后从左往右扫，扫到一个询问的右端点就求最大值即可

#### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
const int MAX=5e5+5;
struct p{
	int l,r,id;
	bool operator< (const p &a) const{return r<a.r;}
}qu[MAX];
int n,q,UP,maxn;
int ans[MAX],a[MAX],p[21],pos[21];
int read()
{
	int x(0);
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
void ins(int x,int id)
{
	for(int i=UP;i>=0;--i)
	  if(x&(1<<i))
	  {
	  	if(!p[i])
	  	{
	  		p[i]=x;
	  		return void(pos[i]=id);
		}
		if(pos[i]<id) swap(p[i],x),swap(pos[i],id);
		x^=p[i];
	  }
}
int GET_MAX(int id)
{
	int Ans=0;
	for(int i=UP;i>=0;--i)
	  if(pos[i]>=id&&(Ans^p[i])>Ans) Ans^=p[i];
	return Ans;
}
int main()
{
	n=read();
	for(int i=1;i<=n;++i)
	  a[i]=read(),maxn=max(maxn,a[i]);
	while(maxn) maxn>>=1,++UP;
	q=read();
	for(int i=1;i<=q;++i)
	  qu[i].l=read(),qu[i].r=read(),qu[i].id=i;
	sort(qu+1,qu+1+q);
	for(int i=1,L=1;i<=q;++i)
	  {
	  	while(L<=qu[i].r&&L<=n) ins(a[L],L),++L;
	  	ans[qu[i].id]=GET_MAX(qu[i].l);
	  }
	for(int i=1;i<=q;++i)
	  printf("%d\n",ans[i]);
	return 0;
}
```

