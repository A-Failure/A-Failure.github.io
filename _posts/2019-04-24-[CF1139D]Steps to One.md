---
layout:		post
title:		"[CF1139D]Steps to One"
date:		2019-04-24
author:		"Dispwnl"
header-img:	"img/used/222539.jpg"
catalog:	false
tags:
    - 动态规划
    - 莫比乌斯反演
    - 概率与期望
---

## [题目](<https://codeforces.com/problemset/problem/1139/D>)

### 题目大意

给定一个空序列$a$和一个整数$m$，每次可以往序列里加入一个数$x(x\in [1,m])$，求当所有数的$\gcd$为$1$时的期望次数

### ### Examples

#### input

```plain
1
```

#### output

```plain
1
```

#### input

```plain
2
```

#### output

```plain
2
```

#### input

```plain
4
```

#### output

```plain
333333338
```

### 题解

加入数之后$\gcd$肯定是单调不升的，设$f_i$表示当前$\gcd$为$i$转移到$1$需要的期望次数，则有

$f_i=1+\frac{\sum_{d\vert i}f_d\sum_{j=1}^{m}[\gcd(i,j)==d]}{m}$

发现里面那一坨$\gcd$很莫比乌斯！直接反演得

$f_i=1+\frac{\sum_{d\vert i}f_d\sum_{x\vert \left\lfloor\frac{i}{d}\right\rfloor\mu(x)\left\lfloor\frac{m}{dx}\right\rfloor}}{m}$

发现当$d=n$的时候，上面的一坨是$\left\lfloor\frac{m}{i}\right\rfloor f_i$，这样可以直接移项求解$f_i$了

因为序列开始是空的，相当于多转移了一步最后的答案就是$1+\frac{\sum_{i=1}^{m}f_i}{m}$

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<vector>
# include<algorithm>
using namespace std;
const int MAX=1e5+5,mod=1e9+7;
int n,ans,tot;
int f[MAX],pm[MAX],mu[MAX],inv[MAX];
bool use[MAX];
vector<int> vec[MAX];
void ss()
{
	mu[1]=1;
	for(int i=2;i<=n;++i)
	  {
	  	if(!use[i]) pm[++tot]=i,mu[i]=-1;
	  	for(int j=1;j<=tot&&i*pm[j]<=n;++j)
	  	  {
	  	  	use[i*pm[j]]=1;
	  	  	if(i%pm[j]==0) break;
	  	  	mu[i*pm[j]]=-mu[i];
		  }
	  }
	inv[1]=1;
	for(int i=1;i<=n;++i)
	  {
	  	if(i!=1) inv[i]=1ll*(mod-mod/i)*inv[mod%i]%mod;
	  	for(int j=i;j<=n;j+=i)
	      vec[j].push_back(i);
	  }
}
int Solve(int x)
{
	int Ans=0;
	for(auto v:vec[x])
	  if(v!=x)
	  {
	  	int ans=0;
	  	for(auto t:vec[x/v])
	  	  (ans+=mu[t]*(n/(t*v)))%=mod;
		(Ans+=1ll*(ans+mod)*f[v]%mod)%=mod;
	  }
	return Ans;
}
int main()
{
	scanf("%d",&n),ss();
	for(int i=2;i<=n;++i)
	  f[i]=1ll*(n+Solve(i))*inv[n-n/i]%mod,(ans+=f[i])%=mod;
	return printf("%d",(1ll*ans*inv[n]%mod+1)%mod),0;
}
```

