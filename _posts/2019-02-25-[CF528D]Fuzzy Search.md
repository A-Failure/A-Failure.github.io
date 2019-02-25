---
layout:		post
title:		"[CF528D]Fuzzy Search"
date:		2019-02-25
author:		"Dispwnl"
header-img:	"img/used/79099.jpg"
catalog:	false
tags:
    - FFT
---

## [题目](http://codeforces.com/problemset/problem/528/D)

### 题目大意

给定两个只由<code>A</code>,<code>C</code>,<code>G</code>,<code>T</code>组成的字符串$s,t$，再给定一个整数$k$，定义两个位置$i,j$能匹配为$t$中位置$i$上字符为$c$，$s$中距离位置$j$不超过$k$的位置上也有字符$c$，求$t$能在$s$上匹配成功多少次

### Examples

#### input

```plain
10 4 1
AGCAATTCAT
ACAT
```

#### output

```plain
3
```
### 题解
因为字符集很小，所以可以枚举字符，如果一个位置（往后匹配）$4$种字符的匹配数为$n$的话就说明这个位置可以匹配成功

处理出来每个位置是否能匹配到字符$c$，然后这样一个位置$l$能匹配成功就是$\sum_{i=0}^{n-1}a_ib_{i+l}=N$，$N$为$t$中字符$c$的数量，这样把$a$倒过来就是个卷积的形式了，$FFT$处理即可

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<cmath>
# include<algorithm>
using namespace std;
const int MAX=1e6+5;
const double Pi=acos(-1.0);
struct Complex{
	double x,y;
	Complex(double X=0,double Y=0) {x=X,y=Y;}
}a[MAX],b[MAX];
int n,m,k,lim=1,L,ans;
int sum[MAX],R[MAX],val[MAX];
char s[MAX],t[MAX];
Complex operator+ (Complex x,Complex y) {return Complex(x.x+y.x,x.y+y.y);}
Complex operator- (Complex x,Complex y) {return Complex(x.x-y.x,x.y-y.y);}
Complex operator* (Complex x,Complex y) {return Complex(x.x*y.x-x.y*y.y,x.y*y.x+x.x*y.y);}
void FFT(Complex *A,int tt=1)
{
	for(int i=0;i<lim;++i)
	  if(i<R[i]) swap(A[i],A[R[i]]);
	for(int i=1;i<lim;i<<=1)
	  {
	  	Complex w1=Complex(cos(Pi/i),tt*sin(Pi/i));
	  	for(int l=i<<1,j=0;j<lim;j+=l)
	  	  {
	  	  	Complex w=Complex(1,0),x,y;
	  	  	for(int k=0;k<i;++k,w=w*w1)
	  	  	  x=A[j+k],y=w*A[i+j+k],A[j+k]=x+y,A[i+j+k]=x-y;
		  }
	  }
}
void Solve(char A)
{
	memset(a,0,sizeof(a));
	memset(b,0,sizeof(b));
	int cnt=0;
	for(int i=0;i<m;++i)
	  if(t[i]==A) a[i].x=1,++cnt;
	for(int i=0;i<n;++i)
	  sum[i+1]=sum[i]+(s[i]==A);
	for(int i=1;i<=n;++i)
	  if(sum[min(i+k,n)]-sum[max(i-k-1,0)]) b[i-1].x=1;
	FFT(a),FFT(b);
	for(int i=0;i<=lim;++i)
	  a[i]=a[i]*b[i];
	FFT(a,-1);
	for(int i=m-1,j=0;i<n+m-1;++i,++j)
	  if(int(a[i].x/lim+0.5)==cnt) val[j]+=cnt;
}
int main()
{
	scanf("%d%d%d%s%s",&n,&m,&k,s,t);
	while(lim<=n+m-2) lim<<=1,++L;
	for(int i=0;i<lim;++i)
	  R[i]=(R[i>>1]>>1)|((i&1)<<L-1);
	reverse(t,t+m),Solve('A'),Solve('C'),Solve('G'),Solve('T');
	for(int i=0;i<n;++i)
	  if(val[i]==m) ++ans;
	return printf("%d",ans),0;
}
```

