---
layout:		post
title:		"[CF1137X]Codeforces Round #545 (Div. 1)"
date:		2019-03-11
author:		"Dispwnl"
header-img:	"img/used/0909.jpg"
catalog:	false
tags:
    - 比赛
---

> 尝试一下打Div 1……

### [A　　Skyscrapers](https://codeforces.com/contest/1137/problem/A)

#### 题目大意

给定一个矩阵，要求求出对每个交点所在的行和列重新离散化所需要的数的个数

#### 题解

处理出来每个交点行比它小的数的个数$N_1$，比它大的数的个数$N_2$，列比它小的数的个数$N_3$，比它大的数的个数$N_4$，那么答案就是$max(N_1,N_3)+max(N_2,N_4)+1$

直接用树状数组扫$4$遍就行了……数组开小<code>WA</code>了一发OAO

#### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
const int N=1e3+5,MAX=1e6+5;
struct p{
	int x,i,j;
	bool operator< (const p &a)
	const{
		return x<a.x;
	}
}A[MAX];
int n,m,id,cnt;
int s[MAX];
int a[N][N],b[N][N],c[N][N],d[N][N],e[N][N];
bool use[MAX];
int lowbit(int x) {return x&(-x);}
void change(int x,int d)
{
	for(int i=x;i<=id;i+=lowbit(i))
	  s[i]+=d;
}
int ask(int x)
{
	int ans=0;
	for(int i=x;i;i-=lowbit(i))
	  ans+=s[i];
	return ans;
}
int ask(int l,int r)
{
	return ask(r)-ask(l-1);
}
int main()
{
	scanf("%d%d",&n,&m);
	for(int i=1;i<=n;++i)
	  for(int j=1;j<=m;++j)
	    scanf("%d",&a[i][j]),A[++cnt]=(p){a[i][j],i,j};
	sort(A+1,A+1+cnt);
	for(int i=1;i<=cnt;++i)
	  {
	  	if(A[i].x!=A[i-1].x) ++id;
	  	a[A[i].i][A[i].j]=id;
	  }
	for(int i=1;i<=n;++i)
	  {
	  	for(int j=1;j<=m;++j)
	  	  if(!use[a[i][j]]) use[a[i][j]]=1,change(a[i][j],1);
	  	for(int j=1;j<=m;++j)
	  	  b[i][j]=ask(1,a[i][j]-1),c[i][j]=ask(a[i][j]+1,id);
	  	for(int j=1;j<=m;++j)
	  	  if(use[a[i][j]]) use[a[i][j]]=0,change(a[i][j],-1);
	  }
	for(int i=1;i<=m;++i)
	  {
	  	for(int j=1;j<=n;++j)
	  	  if(!use[a[j][i]]) use[a[j][i]]=1,change(a[j][i],1);
	  	for(int j=1;j<=n;++j)
	  	  d[j][i]=ask(1,a[j][i]-1),e[j][i]=ask(a[j][i]+1,id);
	  	for(int j=1;j<=n;++j)
	  	  if(use[a[j][i]]) use[a[j][i]]=0,change(a[j][i],-1);
	  }
	for(int i=1;i<=n;++i,printf("\n"))
	  for(int j=1;j<=m;++j)
	    printf("%d ",max(b[i][j],d[i][j])+max(c[i][j],e[i][j])+1);
	return 0;
}
```

### [B　　Camp Schedule](https://codeforces.com/contest/1137/problem/B)

#### 题目大意

给定两个字符串$S,T$，重新组合$S$，使得$T$在$S$中出现次数最多

#### 题解

求出$T$的<code>next</code>的数组，然后就可以贪心了

#### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
const int MAX=5e5+5;
int n,m,n0,n1,cnt;
int nxt[MAX];
char a[MAX],b[MAX],c[MAX];
void GET_NXT()
{
    int k=0;
    nxt[1]=0;
    for(int i=2;i<=m;++i)
      {
      	while(k&&b[k+1]!=b[i]) k=nxt[k];
      	if(b[k+1]==b[i]) ++k;
      	nxt[i]=k;
      }
}
int main()
{
	scanf("%s%s",a+1,b+1);
	n=strlen(a+1),m=strlen(b+1);
	if(m>n) return printf("%s",a+1),0;
	for(int i=1;i<=n;++i)
	  if(a[i]=='0') ++n0;
	  else ++n1;
	GET_NXT();
	for(int i=1,L=1;i<=n;++i)
	  {
	  	if(b[L]=='0')
	  	{
	  		if(!n0)
	  		{
	  			printf("%s",c+1);
	  			for(int j=1;j<=n1;++j)
	  			  printf("1");
	  			return 0;
			}
			c[++cnt]='0',--n0;
		}
		if(b[L]=='1')
	  	{
	  		if(!n1)
	  		{
	  			printf("%s",c+1);
	  			for(int j=1;j<=n0;++j)
	  			  printf("0");
	  			return 0;
			}
			c[++cnt]='1',--n1;
		}
		if(L==m) L=nxt[m];
		++L;
	  }
	return printf("%s",c+1),0;
}
```

### [C　　Museums Tour](https://codeforces.com/contest/1137/problem/C)

#### 题目大意

给定一个$n$个点$m$条边的有向图，一周有$d$天，每个点只有在一周的某些日子有$1$的贡献

现在从$1$号节点出发，同一个点贡献只计入一次，求贡献最大是多少

#### 题解

首先把一个点拆成$d$个点，然后如果原图中有边$u\rightarrow v$，有$(u,a)\rightarrow (v,(a+1)\%d),0\le a<d$，可以给这个新图缩点，如果两个点$(u,a),(v,b)$在一个强连通分量里，说明它们可以通过原图中的一些边（花费一定的时间）相互到达

现在的问题就是如何处理同一个点的贡献只计入一次，发现如果有**路径**$(u,a)\rightarrow (u,b)$，必有路径$(u,b)\rightarrow (u,a)$，即$(u,a)(u,b)$在同一个强连通分量里

设$(a+t)\%d=b$，则有$b+(d-1)t\%d=a+t\%d+(d-1)t\%d=a$

所以一个强连通分量里面去重就行了

然后这题需要卡卡卡卡卡卡卡空间……

#### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<vector>
# include<queue>
# include<algorithm>
using namespace std;
const int MAX=5e6+5,N=1e5+5;
int n,m,d,num,cnt,Top,ans,Ans;
int du[MAX],h[MAX],col[MAX],val[MAX],f[MAX],low[MAX],dfn[MAX],st[MAX],nxt[MAX],to[MAX],X[N],Y[N];
char a[55];
bool use[MAX],vis[MAX];
vector<int> vec[MAX];
queue<int> qu;
int read()
{
	int x(0);
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
void Tarjan(int x)
{
	low[x]=dfn[x]=++cnt,st[++Top]=x,use[x]=1;
	for(int i=h[x];i;i=nxt[i])
	  if(!dfn[to[i]]) Tarjan(to[i]),low[x]=min(low[x],low[to[i]]);
	  else if(use[to[i]]) low[x]=min(low[x],dfn[to[i]]);
	if(low[x]==dfn[x])
	{
		int tt=-1;
		++ans;
		while(tt!=x)
		{
			tt=st[Top--],use[tt]=0,col[tt]=ans;
			if(val[tt]) vec[ans].push_back(du[tt]);
		}
	}
}
int max(int x,int y) {return x>y?x:y;}
void add(int x,int y) {nxt[++num]=h[x],to[num]=y,h[x]=num;}
void Add(int x,int y) {++du[y],nxt[++num]=h[x],to[num]=y,h[x]=num;}
void Solve()
{
	for(int i=1;i<=ans;++i)
	  {
	  	f[i]=-1e9;
	  	if(!du[i]) qu.push(i);
	  }
	f[col[1]]=val[col[1]];
	while(!qu.empty())
	{
		int tt=qu.front();
		qu.pop();
		for(int i=h[tt];i;i=nxt[i])
		  {
		  	if(f[tt]>-1e9) f[to[i]]=max(f[to[i]],f[tt]+val[to[i]]);
		  	if(!--du[to[i]]) qu.push(to[i]);
		  }
	}
	for(int i=1;i<=ans;++i)
	  Ans=max(Ans,f[i]);
}
int main()
{
//	freopen("tt.in","r",stdin);
	n=read(),m=read(),d=read();
	for(int i=1;i<=m;++i)
	  {
	  	X[i]=read(),Y[i]=read();
		for(int j=0;j<d;++j)
	  	  add(X[i]+j*n,Y[i]+(j+1)%d*n);
	  }
	for(int i=1;i<=n;++i)
	  {
	  	scanf("%s",a);
	  	for(int j=0;j<d;++j)
	  	  du[i+j*n]=i,val[i+j*n]=a[j]-'0';
	  }
	for(int i=1;i<=d*n;++i)
	  if(!dfn[i]) Tarjan(i);
	for(int i=1;i<=ans;++i)
	  {
	  	val[i]=0;
	  	if(vec[i].size())
		{
	  		sort(vec[i].begin(),vec[i].end()),++val[i];
	  		for(int j=1,cnt=vec[i].size();j<cnt;++j)
	  		  if(vec[i][j]!=vec[i][j-1]) ++val[i];
		}
	  }
	num=0;
	memset(h,0,sizeof(h));
	memset(du,0,sizeof(du));
	for(int i=1;i<=m;++i)
	  for(int j=0;j<d;++j)
	  	if(col[X[i]+j*n]!=col[Y[i]+(j+1)%d*n]) Add(col[X[i]+j*n],col[Y[i]+(j+1)%d*n]);
	return Solve(),printf("%d",Ans),0;
}
```

### [D　　Cooperative Game](https://codeforces.com/contest/1137/problem/D)

#### 题目大意

交互题

现在有一个长度为$t$的有向链连着一个长度为$c$的有向简单环，链头为起点，链与环的连接点为终点，现在有$10$个点在起点位置，每次可以移动指定的几个点往前走一步，然后查询，返回现在有多少个节点上有点，和每个有点节点上的点是哪些，最多询问$3(t+c)$次

#### 题解

先移动两个点，每次点$1$移动一次，点$2$移动两次，这样只用花$2c$次询问$1,2$点重合并且在环上并且都在节点$c-t$上

这样在移动所有的$10$个点$t$次，所有点就会重合在终点了，总次数$2c+t$

#### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
char a[15];
int ask()
{
	int n;
	scanf("%d",&n);
	for(int i=1;i<=n;++i)
	  scanf("%s",a);
	return n;
}
void Puts() {puts("next 8 9"),fflush(stdout);}
void Puts_() {puts("next 8"),fflush(stdout);}
void Puts__() {puts("next 0 1 2 3 4 5 6 7 8 9"),fflush(stdout);}
int main()
{
	int n=10;
	while(n>2) Puts(),ask(),Puts_(),n=ask();
	while(n>1) Puts__(),n=ask();
	return puts("done"),fflush(stdout),0;
}
```

### [E　　Train Car Selection](https://codeforces.com/contest/1137/problem/E)

#### 题目大意

给定一个序列，每个位置的值为$b+s(i-1)$初始值都是$0$，有$3$种操作：

- 序列头加入$k$个位置，值都为$0$
- 序列尾加入$k$个位置，值都为$0$
- $b+x,s+y$

每次操作完要求输出当前序列中的最小值和位置

#### 题解

> emmm我开始以为是个$Fhq\ Treap$然后我看到了数据范围$1e9$……
>
> 打扰了

发现每一个加入的位置都可以看成一个点（因为加入的最左边点肯定最优）

头上加的位置肯定比后面的优，所以把后面的都去掉

这些位置可以看成一个一个的点，然后维护一个上凸壳就行了

#### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
# define LL long long
using namespace std;
const int MAX=3e5+5;
struct Point{
	LL x,y;
	Point(LL X=0,LL Y=0) {x=X,y=Y;}
}_P[MAX];
int m,Top;
LL n,k,d;
double K(Point a,Point b) {return double(a.y-b.y)/double(a.x-b.x);}
int main()
{
	scanf("%I64d%d",&n,&m),Top=1;
	for(int i=1,op,x,y;i<=m;++i)
	  {
	  	scanf("%d",&op);
	  	if(op==1) scanf("%d",&x),Top=1,k=d=0,_P[1]=Point(0,0),n+=x;
	  	else if(op==2)
	  	{
	  		scanf("%d",&x);
			Point A=Point(n,-n*k-d);
	  		while(Top>1&&K(_P[Top],A)<=K(_P[Top-1],_P[Top])) --Top;
	  		_P[++Top]=A,n+=x;
		}
		else if(op==3) scanf("%d%d",&x,&y),d+=x,k+=y;
		while(Top>1&&_P[Top].y+_P[Top].x*k>=_P[Top-1].y+_P[Top-1].x*k) --Top;
		printf("%I64d %I64d\n",_P[Top].x+1,_P[Top].y+_P[Top].x*k+d);
	  }
	return 0;
}
```

