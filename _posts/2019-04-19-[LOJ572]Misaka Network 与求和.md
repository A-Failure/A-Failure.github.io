---
layout:		post
title:		"[LOJ572]Misaka Network 与求和"
date:		2019-04-19
author:		"Dispwnl"
header-img:	"img/used/63577042.jpg"
catalog:	false
tags:
    - 杜教筛
    - min_25筛
    - 莫比乌斯反演
---

## [题目](<https://loj.ac/problem/572>)

### 题解

发现这个式子非常莫比乌斯，设$F(x)=f(x)^k​$，先化式子

$\sum_{i=1}^{n}{\left\lfloor\frac{n}{i}\right\rfloor}^2\sum_{d\vert i}F(d)\mu(\frac{i}{d})​$

后面的是个狄利克雷卷积的形式，尝试用杜教筛筛一下前缀和

设$s(n)=\sum_{i=1}^{n}(F*\mu)(i)​$，于某不知名函数$t(x)​$卷积后有式子

$t(1)s(n)=\sum_{i=1}^{n}(F*\mu*t)(i)-\sum_{i=2}^{n}t(i)s(\left\lfloor\frac{n}{i}\right\rfloor)​$

有$\mu​$，考虑$t=1​$，这样式子就成了$s(n)=\sum_{i=1}^{n}F(i)-\sum_{i=2}^{n}s(\left\lfloor\frac{n}{i}\right\rfloor)​$

再考虑如何求$\sum_{i=1}^{n}F(i)$，尝试用min_25筛筛一下前缀和

设$S(n,j)$为上一个质因子选$P_{j-1}$且剩下的数的积不超过$n$的答案，有式子

$S(n,j)={P_{j-1}}^k\sum_{i=P_{j-1}+1}^{n}[i\in P]+\sum_{i=1}^{{P_i}\le n}\sum_{c=1}^{P_i\le n}S(\left\lfloor\frac{n}{{P_i}^c}\right\rfloor)+{P_i}^k$

这样就可以搞了

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<cmath>
# include<algorithm>
# define W (w[i]/pm[j])
# define uint unsigned int
using namespace std;
const int MAX=2e5+5;
int n,k,N,tot,cnt;
int pm[MAX],w[MAX],g[MAX],id1[MAX],id2[MAX];
uint val[MAX],Pk[MAX];
bool use[MAX],vis[MAX];
uint Pow(uint a,int b)
{
	uint ans=1;
	for(;b;b>>=1,a*=a)
	  if(b&1) ans*=a;
	return ans;
}
void ss()
{
	for(int i=2;i<=N;++i)
	  {
	  	if(!use[i]) pm[++tot]=i,Pk[tot]=Pow(pm[tot],k);
	  	for(int j=1;i*pm[j]<=N&&j<=tot;++j)
	  	  {
	  	  	use[i*pm[j]]=1;
	  	  	if(i%pm[j]==0) break;
		  }
	  }
}
uint S(int m,int x)
{
	if(m<=1||pm[x]>m) return 0;
	int l=(m<=N)?id1[m]:id2[n/m],P;
	uint ans=Pk[x-1]*(g[l]-(x-1));
	for(int i=x;i<=tot&&pm[i]*pm[i]<=m;++i)
	  for(P=pm[i];1ll*P*pm[i]<=m;P*=pm[i])
	    ans+=S(m/P,i+1)+Pk[i];
	return ans;
}
uint S(int m)
{
	if(!m) return 0;
	int l=(m<=N)?id1[m]:id2[n/m];
	if(vis[l]) return val[l];
	uint ans=S(m,1)+g[l];
	for(int l=2,r;l<=m;l=r+1)
	  r=m/(m/l),ans-=S(m/l)*(r-l+1);
	return vis[l]=1,val[l]=ans;
}
int main()
{
	scanf("%d%d",&n,&k),N=sqrt(n),ss();
	for(int l=1,r;l<=n;l=r+1)
	  {
	  	r=n/(w[++cnt]=n/l);
	  	if(w[cnt]<=N) id1[w[cnt]]=cnt;
	  	else id2[n/w[cnt]]=cnt;
	  	g[cnt]=w[cnt]-1;
	  }
	for(int j=1;j<=tot;++j)
	  for(int i=1,l;i<=cnt&&pm[j]*pm[j]<=w[i];++i)
	    l=(W<=N)?id1[W]:id2[n/W],g[i]-=g[l]-(j-1);
	uint L=0,R=0,ans=0;
	for(int l=1,r;l<=n;l=r+1)
	  r=n/(n/l),ans+=((R=S(r))-L)*(n/l)*(n/l),L=R;
	return printf("%u",ans),0;
}
```

