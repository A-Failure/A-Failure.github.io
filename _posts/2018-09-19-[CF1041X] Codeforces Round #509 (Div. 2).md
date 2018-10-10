---
layout:     post
title:      "[CF1041X] Codeforces Round #509 (Div. 2)"
date:       2018-09-19
author:     "Dispwnl"
header-img: "img/used/99.jpg"
catalog: true
tags:
    - 比赛
---
> 感觉这场CF偏水QAQ

>恭喜$fuge$喜提紫名

### [A　　Heist](http://codeforces.com/contest/1041/problem/A)
#### 题目大意
一堆数，要你找出从小到大排序后中间缺少了多少个数

#### 题解
。

#### 代码
```
# include<bits/stdc++.h>
using namespace std;
const int MAX=1e4+1;
int n;
int a[MAX];
int main()
{
	scanf("%d",&n);
	for(int i=1;i<=n;++i)
	  scanf("%d",&a[i]);
	sort(a+1,a+1+n);
	int ans=0;
	for(int i=2;i<=n;++i)
	  ans+=a[i]-a[i-1]-1;
	return printf("%d",ans),0;
}
```
### [B　　Buying a TV Set](http://codeforces.com/contest/1041/problem/B)
#### 题目大意
给你$a,b,c,d$，求满足$\frac{x}{y}=\frac{c}{d},x\leq a,y\leq b$的$x,y$的对数

#### 题解
。。
#### 代码
```
# include<bits/stdc++.h>
# define LL long long
using namespace std;
LL a,b,x,y;
LL gcd(LL a,LL b)
{
	return b?gcd(b,a%b):a;
}
int main()
{
	cin>>a>>b>>x>>y;
	LL GCD=gcd(x,y);
	x=x/GCD,y=y/GCD;
	LL A1=a/x,B1=b/y;
	return cout<<min(A1,B1),0;
}
```
### [C　　Coffee Break](http://codeforces.com/contest/1041/problem/C)
#### 题目大意
给定$n$个数和一个$k$，每次从还存在的数里面选一个数$a$，去掉$a$，然后可以任意一个$b(b>a+k)$，然后去掉任意一个$c(c>b+k)$，以此类推

问最少能选多少个$a$，然后输出每个数都是选第几个$a$的时候被去掉的

#### 题解
根据贪心的思想，每次选的$a,b,c...$肯定要往小里面选

排下序，拿个并查集存大于这个数$+k$后第一个没被选过的点，这样就形成一片并查集森林

统计森林里有几棵树即可，开始数组开小了RE了QAQ

#### 代码
```
# include<bits/stdc++.h>
using namespace std;
const int MAX=2e5+5;
struct p{
	int x,id;
	bool operator< (const p &a)
	const{
		return x<a.x;
	}
}a[MAX];
int n,m,d;
int fa[MAX],ID[MAX],IID[MAX],ans[MAX];
bool use[MAX];
int find(int x)
{
	if(fa[x]!=x) fa[x]=find(fa[x]);
	return fa[x];
}
int main()
{
	scanf("%d%d%d",&n,&m,&d);
	for(int i=1;i<=n;++i)
	  scanf("%d",&a[i].x),a[i].id=i,fa[i]=i;
	int L=1;
	sort(a+1,a+1+n);
	for(int i=1;i<=n;++i)
	  {
	  	ID[i]=a[i].id,++L;
	  	while(L<=n&&a[L].x<=a[i].x+d) ++L;
	  	if(L<=n)
	  	{
	  		int r1=find(a[i].id),r2=find(a[L].id);
	  		fa[r2]=r1;
		}
	  }
	int num=0;
	for(int i=1;i<=n;++i)
	  {
	  	int r1=find(a[i].id);
	  	if(!use[r1]) use[r1]=1,IID[r1]=++num;
	  }
	printf("%d\n",num);
	for(int i=1;i<=n;++i)
	  {
	  	int r1=find(ID[i]);
	  	ans[ID[i]]=IID[r1];
	  }
	for(int i=1;i<=n;++i)
	  printf("%d ",ans[i]);
	return 0;
}
```
### [D　　Glider](http://codeforces.com/contest/1041/problem/D)
#### 题目大意
有$n$个从$x_i$到$y_i$的上升气流，飞机在上升气流里水平飞行，否则每往前飞行一个单位长度机身就向下掉一个单位长度

飞机可以在任意一个位置以高度$h$出发，问最多能飞多远

#### 题解
求一个前缀和，双指针随便搞搞就好了（%%%$Ycr$）……调了一阵子

#### 代码
```
# include<bits/stdc++.h>
using namespace std;
const int MAX=2e5+5;
struct p{
	int x,y,l;
}a[MAX];
int n,h;
int sum[MAX];
int main()
{
	scanf("%d%d",&n,&h);
	for(int i=1;i<=n;++i)
	  scanf("%d%d",&a[i].x,&a[i].y),a[i].l=a[i].y-a[i].x,sum[i]=sum[i-1]+a[i].x-a[i-1].y;
	int L=2,ans=h;
	for(int i=1;i<=n;++i)
	  {
	  	while(L<=n&&sum[L]-sum[i]<=h) ++L;
	  	--L;
	  	if(sum[L]-sum[i]==h) ans=max(ans,a[L].x-a[i].x);
	  	else ans=max(ans,a[L].x-a[i].x+a[L].l+h-sum[L]+sum[i]);
	  }
	return printf("%d",ans),0;
}
```
### [E　　Tree Reconstruction](http://codeforces.com/contest/1041/problem/E)
#### 题目大意
有一棵树，现在给你每条树边被去掉时，形成的两个联通块中点的最大的编号分别是多少，问满足条件的树存不存在

#### 题解
显然如果能构成的话某个正确答案肯定是一条链，并且给出的两个数肯定有一个是$n$（可以判能否构成答案）

如果我们把$n$放在链尾，如果数$a$出现过，$a$前面的数肯定都小于$a$；如果一个数$b$出现了多次，说明ta挤掉了某些数，被挤掉的数肯定小于$b$并且在$b$后面

#### 代码
```
# include<bits/stdc++.h>
using namespace std;
const int MAX=1e3+1;
int n,num;
int a[MAX],ans[MAX],unuse[MAX];
bool use[MAX];
int main()
{
	scanf("%d",&n);
	for(int i=1,x,y;i<n;++i)
	  {
	  	scanf("%d%d",&x,&y);
	  	if(x>y) swap(x,y);
	  	if(y!=n) return printf("NO"),0;
	  	a[i]=x,use[x]=1;
	  }
	sort(a+1,a+n);
	for(int i=1;i<n;++i)
	  if(!use[i]) unuse[++num]=i;
	int L=0;
	for(int i=1;i<n;++i)
	  if(a[i]==a[i-1])
	  {
	  	ans[i]=unuse[++L];
	  	if(unuse[L]>a[i]) return printf("NO"),0;
	  }
	  else ans[i]=a[i];
	printf("YES\n");
	for(int i=1;i<n-1;++i)
	  printf("%d %d\n",ans[i],ans[i+1]);
	return printf("%d %d",ans[n-1],n),0;
}
```
### [F　　Ray in the tube](http://codeforces.com/contest/1041/problem/F)
#### 题目大意
下边界有$n$个给定点，上边界有$m$个给定点，可以从任意一个点发出一条激光，激光碰到边界会反射

激光到达边界必须打到整数点，问最多可以打到几个给定点

#### 题解
发现以$2^n$为步长发射激光肯定是最优的，因为点坐标的奇偶性肯定能找出奇$->$奇或者偶$->$偶这种组合……好吧具体我也不会证QAQ

注意有步长为$0$这种情况

#### 代码
```
# include<bits/stdc++.h>
using namespace std;
const int MAX=1e5+1;
int n,m,y,_y,ans;
int x[MAX],_x[MAX];
map<int,int> ma,Ma;
int main()
{
	scanf("%d%d",&n,&y);
	for(int i=1;i<=n;++i)
	  scanf("%d",&x[i]),Ma[x[i]]=1;
	scanf("%d%d",&m,&_y);
	for(int i=1;i<=m;++i)
	  {
	  	scanf("%d",&_x[i]);
	  	if(Ma[_x[i]]==1) ans=2;
	  }
	for(int tt=2;tt<=1e9;tt<<=1)
	  {
	  	ma.clear();
	  	for(int i=1;i<=n;++i)
	  	  ans=max(ans,++ma[x[i]%tt]);
	  	for(int i=1;i<=m;++i)
	  	  ans=max(ans,++ma[(_x[i]+(tt>>1))%tt]);
	  }
	return printf("%d",ans),0;
}
```
