---
layout:		post
title:		"[BZOJ3512]DZY Loves Math IV"
date:		2019-02-24
author:		"Dispwnl"
header-img:	"img/used/344666.jpg"
catalog:	false
tags:
    - 杜教筛
---

## [题目](https://lydsy.com/JudgeOnline/problem.php?id=3512)

### Description

给定n,m，求![img](https://lydsy.com/JudgeOnline/upload/201404/111.jpg) 模10^9+7的值。

### Input

仅一行，两个整数n,m。

### Output

仅一行答案。

### Sample Input
```plain
100000 1000000000
```

### Sample Output
```plain
857275582
```
### 数据规模：
1<=n<=10^5，1<=m<=10^9，本题共4组数据。

### 题解

感觉我自己是不可能推出这样的式子的……

因为有式子$\phi(ij)=\phi(i)j,\gcd(i,j)!=1$

$\phi(ij)=\phi(i)\phi(j),gcd(i,j)=1$

设$S_{n,m}​$为$\sum_{i=1}^{m}\phi(ni)​$

分解$n​$为$\prod_{i=1}^{k}p_i^{a_i}​$，设$P=\prod_{i=1}^{k}p_i^{a_i-1},P'=\frac{n}{P}​$

则$P$和$P'$肯定不互质（特殊地，$P=1$时是互质的，但是$\phi(1)=1$，方便起见看成不互质了），则有$\sum_{i=1}^{m}\phi(ni)=P\sum_{i=1}^{m}\phi(P'i)$

提出$P'$和$i$的$\gcd$，$\frac{P'}{\gcd(P',i)}$和$i$肯定互质，则有$P\sum_{i=1}^{m}\phi(P'i)=P\sum_{i=1}^{m}\phi(\frac{P'}{\gcd(P',i)})\phi(i)\gcd(P',i)$

即$S_{n,m}​$

$=P\sum_{i=1}^{m}\phi(\frac{P'}{\gcd(P',i)})\phi(i)\sum_{d\vert \gcd(P',i)}\phi(d)​$

$=P\sum_{i=1}^{m}\phi(i)\sum_{d\vert gcd(P',i)}\phi(\frac{P'd}{\gcd(P',i)})$

$=P\sum_{d\vert P'}\phi(\frac{P'}{d})\sum_{i=1}^{\left\lfloor\frac{m}{d}\right\rfloor}\phi(id)$

$=P\sum_{d\vert P'}\phi(\frac{P'}{d})S_{d,\left\lfloor\frac{m}{d}\right\rfloor}$

枚举$n$，然后记忆化就能做了

复杂度什么鬼啊……根本不会分析

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<vector>
# include<cmath>
# include<map>
# include<algorithm>
using namespace std;
const int MAX=1e5+5,N=5e6+5,mod=1e9+7;
int n,m,tot,ans;
int phi[N],_phi[N],pm[N];
bool use[N],vis[MAX];
map<int,int> ph[MAX],Ph;
void dfs(int x)
{
	if(x<N) return;
	if(Ph.find(x)!=Ph.end()) return;
	int ans=(1ll*x*(x+1)>>1)%mod;
	for(int l=2,r,_N;l<=x;l=r+1)
	  _N=x/l,r=x/_N,dfs(_N),ans=(ans-1ll*(r-l+1)*(_N<N?_phi[_N]:Ph[_N])%mod+mod)%mod;
	Ph[x]=ans;
}
void ss(int n)
{
	phi[1]=_phi[1]=1;
	for(int i=2;i<=n;++i)
	  {
	  	if(!use[i]) pm[++tot]=i,phi[i]=i-1;
	  	for(int j=1;j<=tot&&pm[j]*i<=n;++j)
	  	  {
	  	  	use[pm[j]*i]=1;
	  	  	if(i%pm[j]==0) {phi[pm[j]*i]=phi[i]*pm[j];break;}
	  	  	phi[pm[j]*i]=phi[i]*(pm[j]-1);
		  }
		_phi[i]=_phi[i-1]+phi[i];
		if(_phi[i]>=mod) _phi[i]-=mod;
	  }
}
int S(int n,int m)
{
	if(!m) return 0;
	if(n==1) return (m<N)?_phi[m]:Ph[m];
	if(m==1) return phi[n];
	if(ph[n].find(m)!=ph[n].end()) return ph[n][m];
	vector<int> vec;
	int P=1,_P=1,_N=n,ans=0;
	for(int i=2;i<=sqrt(_N);++i)
	  if(_N%i==0)
	  {
	  	P*=i,_N/=i,vec.push_back(i);
	  	while(_N%i==0) _N/=i,_P*=i;
	  }
	if(_N!=1) vec.push_back(_N),P*=_N;
	for(int i=0,cnt=vec.size(),d;i<(1<<cnt);++i)
	  {
	  	d=1;
	  	for(int j=0;j<cnt;++j)
	  	  if(i&(1<<j)) d*=vec[j];
	  	ans+=1ll*phi[P/d]*S(d,m/d)%mod;
		if(ans>=mod) ans-=mod;
	  }
	return ph[n][m]=1ll*ans*_P%mod;
}
int main()
{
	scanf("%d%d",&n,&m),ss(min(max(n,m),N-1)),dfs(m);
	for(int i=1;i<=n;++i)
	  {
	  	ans+=S(i,m);
	  	if(ans>=mod) ans-=mod;
	  }
	return printf("%d",ans),0;
}
```



