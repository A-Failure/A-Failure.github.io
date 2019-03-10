---
layout:		post
title:		"[CF438E]The Child and Binary Tree"
date:		2019-03-04
author:		"Dispwnl"
header-img:	"img/used/345746.jpg"
catalog:	false
tags:
    - FFT/NTT
    - 生成函数
    - 多项式运算

---

## [题目](http://codeforces.com/problemset/problem/438/E)

### 题目大意

给定$n$个值，要求生成一棵$k$个点的二叉树，树上每个点的权值为给定的$n$个值中的某一个，求树的权值和恰好为$x$的方案数，对$998244353$取模，要求对每个$x\in[1,m]$都输出答案

### Examples

#### input

```plain
2 3
1 2
```

#### output

```plain
1
3
9
```

#### input

```plain
3 10
9 4 3
```

#### output

```plain
0
0
1
1
0
2
4
2
6
15
```

#### input

```plain
5 10
13 10 6 4 15
```

#### output

```plain
0
0
0
1
0
1
0
2
0
5
```

### 题解

如果用$g_i​$表示值$i​$是否能选，$f_i​$表示构成权值和为$i​$的树的方案数，则有

$f_t=\sum_{i=1}^{t}g_i\sum_{j=0}^{t-i}f_jf_{t-i-j},f_0=1$

相当于枚举一个权值为$i​$的点作为根，然后它的两个子树的组合情况

用$F(x)$表示$f$的生成函数$\sum_{i=0}^{∞}f_ix^i$，$G(x)$表示$g$的生成函数$\sum_{i=0}^{∞}g_ix^i​$，则有

$F(t)=1+\sum_{d=0}^{∞}x^d\sum_{i=1}^{d}g_i\sum_{j=0}^{d-i}f_jf_{d-i-j}$

即

$F(t)=1+G(x)F^2(x)$

解方程得

$F(x)=\frac{1-\sqrt{1-4G(x)}}{2G(x)}=\frac{1-1+4G(x)}{2G(x)(1+\sqrt{1-4G(x)})}=\frac{2}{1+\sqrt{1-4G(x)}}$

这样就可以多项式运算了

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
const int MAX=4e5+5,mod=998244353,inv2=499122177;
int n,m,lim=1,L;
int inv[MAX],R[MAX],C[MAX],a[MAX],b[MAX],c[MAX],d[MAX];
int Pow(int a,int b)
{
	int ans=1;
	for(;b;b>>=1,a=1ll*a*a%mod)
	  if(b&1) ans=1ll*ans*a%mod;
	return ans;
}
void NTT(int *A,int tt=1)
{
	for(int i=0;i<lim;++i)
	  if(i<R[i]) swap(A[i],A[R[i]]);
	for(int i=1,w1;i<lim;i<<=1)
	  {
	  	w1=inv[i<<1];
	  	for(int l=i<<1,j=0,w;j<lim;j+=l)
	  	  {
	  	  	w=1;
	  	  	for(int k=0,x,y;k<i;++k,w=1ll*w*w1%mod)
	  	  	  {
	  	  	  	x=A[j+k],y=1ll*w*A[i+j+k]%mod,A[j+k]=x+y,A[i+j+k]=x-y+mod;
	  	  	  	if(A[j+k]>=mod) A[j+k]-=mod;
	  	  	  	if(A[i+j+k]>=mod) A[i+j+k]-=mod;
			  }
		  }
	  }
	if(tt==-1)
	{
		reverse(A+1,A+lim);
		for(int i=0,G=Pow(lim,mod-2);i<lim;++i)
		  A[i]=1ll*A[i]*G%mod;
	}
}
int read()
{
	int x(0);
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
void Inv(int *A,int *b,int n)
{
	if(n==1) return void(b[0]=Pow(A[0],mod-2));
	Inv(A,b,n+1>>1),lim=1,L=0;
	while(lim<=2*n-1) lim<<=1,++L;
	for(int i=0;i<=lim;++i)
	  a[i]=0,R[i]=(R[i>>1]>>1)|((i&1)<<L-1);
	for(int i=0;i<n;++i)
	  a[i]=A[i];
	NTT(a),NTT(b);
	for(int i=0;i<=lim;++i)
	  b[i]=1ll*(2-1ll*a[i]*b[i]%mod+mod)%mod*b[i]%mod;
	NTT(b,-1);
	for(int i=n;i<=lim;++i)
	  b[i]=0;
}
void Sqrt(int *A,int *b,int n)
{
	if(n==1) return void(b[0]=1);
	Sqrt(A,b,n+1>>1);
	for(int i=0;i<=n<<1;++i)
	  c[i]=0;
	Inv(b,c,n),lim=1,L=0;
	while(lim<=2*n-1) lim<<=1,++L;
	for(int i=0;i<=lim;++i)
	  a[i]=0,R[i]=(R[i>>1]>>1)|((i&1)<<L-1);
	for(int i=0;i<n;++i)
	  a[i]=A[i];
	NTT(a),NTT(c);
	for(int i=0;i<=lim;++i)
	  a[i]=1ll*a[i]*c[i]%mod*inv2%mod;
	NTT(a,-1);
	for(int i=0;i<n;++i)
	  {
	  	b[i]=1ll*b[i]*inv2%mod+a[i];
	  	if(b[i]>=mod) b[i]-=mod;
	  }
}
int main()
{
	n=read(),m=read();
	for(int i=1;i<=n;++i)
	  C[read()]=mod-4;
	for(int i=1,N=m<<2;i<=N;i<<=1)
	  inv[i]=Pow(3,(mod-1)/i);
	++C[0],Sqrt(C,d,m+1),d[0]=(d[0]+1)%mod;
	memset(c,0,sizeof(c));
	Inv(d,c,m+1);
	for(int i=1;i<=m;++i)
	  printf("%d\n",2ll*c[i]%mod);
	return 0;
}
```


