---
layout:		post
title:		"[CF954I]Yet Another String Matching Problem"
date:		2019-02-27
author:		"Dispwnl"
header-img:	"img/used/23535.jpg"
catalog:	false
tags:
    - FFT/NTT
    - 并查集
---

## [题目](http://codeforces.com/problemset/problem/954/I)

### 题目大意

两个相同长度的字符串，每次选择一种字符$c$，把两个字符串中所有的字符$c$变成另一种字符，定义两个字符串之间的值为通过以上操作把两个字符串变成一样的最少操作次数

给定字符串$S,T(\vert S\vert\ge\vert T\vert)$，求$S$中每个$\vert T\vert $长度的子串和$T$的值是多少

### ### Example

#### input

```plain
abcdefa
ddcb
```

#### output

```plain
2 3 3 3 
```

### 题解

考虑两个字符串相同长度该怎么快速求值

发现如果两个相对位置（指在自己所属的字符串里的位置）的字符不相同，一定有一个时刻通过**一次操作**使得这两个字符种类相同，把两个字符串扫一遍，遇到不同的就合并两个字符种类对应的并查集，最后答案就是并查集合并次数

不是相对位置的字符显然不需要合并

发现这种方法对字符串的遍历顺序没有要求

现在要求的多个字符串和$T$之间的值，因为字符集很小，考虑枚举两个字符$x,y$，如果$T_i=x,S_{i+j}=y$，说明这两个位置可以进行一次合并操作（是否已经在一个集合里面未知）

发现好像可以搞成这样$\sum_{i=1}^{\vert T\vert}[T_i==x][S_{i+j}==y]$，如果这个值大于$0$，说明$S$中$j$位置开始的子串中的$y$可以和$T$中的$x$进行合并（有相对位置的$x$对$y$），这样把$T$倒过来就是个卷积的形式，用$FFT$优化就可以了，因为遍历顺序没有要求，所以枚举字符顺序对答案也没有影响

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<cmath>
# include<algorithm>
using namespace std;
const int MAX=5e5+5;
const double Pi=acos(-1.0);
struct Complex{
	double x,y;
	Complex(double X=0,double Y=0) {x=X,y=Y;}
}a[MAX],b[MAX];
int n,m,lim=1,L;
int R[MAX],ans[MAX];
int fa[MAX][6];
char A[MAX],B[MAX];
Complex operator+ (Complex x,Complex y) {return Complex(x.x+y.x,x.y+y.y);}
Complex operator- (Complex x,Complex y) {return Complex(x.x-y.x,x.y-y.y);}
Complex operator* (Complex x,Complex y) {return Complex(x.x*y.x-x.y*y.y,x.y*y.x+x.x*y.y);}
int find(int *fa,int x)
{
	if(fa[x]!=x) fa[x]=find(fa,fa[x]);
	return fa[x];
}
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
void Solve(int x,int y)
{
	memset(a,0,lim<<4);
	memset(b,0,lim<<4);
	int cnt1=0,cnt2=0;
	for(int i=0;i<n;++i)
	  if(A[i]-'a'==x) a[i].x=1,++cnt1;
	if(!cnt1) return;
	for(int i=0;i<m;++i)
	  if(B[i]-'a'==y) b[i].x=1,++cnt2;
	if(!cnt2) return;
	FFT(a),FFT(b);
	for(int i=0;i<=lim;++i)
	  a[i]=a[i]*b[i];
	FFT(a,-1);
	for(int i=n-1,j=0,r1,r2;i<m;++i,++j)
	  if(int(a[i].x/lim+0.5))
	  {
	  	r1=find(fa[j],x),r2=find(fa[j],y);
	  	if(r1!=r2) ++ans[j],fa[j][r1]=r2;
	  }
}
int main()
{
	scanf("%s%s",B,A),n=strlen(A),m=strlen(B);
	reverse(A,A+n);
	while(lim<=n+m-2) lim<<=1,++L;
	for(int i=0;i<=lim;++i)
	  R[i]=(R[i>>1]>>1)|((i&1)<<L-1);
	for(int i=0;i<m-n+1;++i)
	  for(int j=0;j<6;++j)
	    fa[i][j]=j;
	for(int i=0;i<6;++i)
	  for(int j=0;j<6;++j)
	    if(i!=j) Solve(i,j);
	for(int i=0;i<m-n+1;++i)
	  printf("%d ",ans[i]);
	return 0;
}
```

