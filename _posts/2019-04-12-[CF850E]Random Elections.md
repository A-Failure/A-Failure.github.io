---
layout:		post
title:		"[CF850E]Random Elections"
date:		2019-04-12
author:		"Dispwnl"
header-img:	"img/used/"
catalog:	false
tags:
    - FWT
---

## [题目](<http://codeforces.com/problemset/problem/850/E>)

### 题目大意

~~直接粘的luogu翻译~~

总统选举将于明年在Bearland举行！每个人都为此感到非常兴奋！

到目前为止，有三位候选人，A，B和C。

Bearland有n个公民。选举结果将对Bearland所有公民的生活产生很大的影响。由于这一重大责任，每个公民都会随机选择A，B和C之间的六个优先顺序（ABC,ACB,BCA,BAC,CBA,CAB）中的一个。如果该顺序为ABC，表示该选民最支持A，其次是B，最不支持C。

考虑到选民的偏好，Bearland政府已经设计了一个用来确定选举结果的函数$f$。

更具体地说，这个函数需要输入一个长度为$2^n$的01串$x$，并返回一个bool。数据保证$f$满足

$f(1-x_1,1-x_2,\ldots,1-x_n)=1-f(x_1,x_2,\ldots,x_n)$

政府将进行3次比赛：A和B、B和C、C和A

在每一次比赛中（假设是候选人A和B之间的），如果第i个人更支持A，则$x_{i}=1$，否则$x_{i}=0$。假设函数f返回的值是1，则A胜，否则B胜

定义$p$为有一个候选人赢了两场的概率，你需要输出$p\times6^n$模1e9+7的值

### Examples

#### input

```plain
3
01010101
```

#### output

```plain
216
```

#### input

```plain
3
01101001
```

#### output

```plain
168
```

### 题解

~~什么gs题意啊~~

因为$A,B,C$三个人没有区别，所以答案就是一个人的方案数$\times 3$

这里以$A$为例，如果$A$想获胜，$f(A跟B)=1$并且$f(A跟C)=1$

假设$s_1s_2$表示这个选民比较$A$和$B$的结果为$s1$，$A$和$C$的结果为$s2$

所以$01$对应着$BAC$，$10$对应着$CAB$，$11$对应着$ABC$或者$ACB$，$00$对应着$BCA$或者$CBA$

只有$\oplus$为$0$的才能产生$2$的情况，所以异或卷积一下即可

然后取模取错地方了<code>WA</code>了好几发？

![](/img/qaq/？？？.jpg)

### 代码

```c++
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
const int MAX=(1<<20)+5,mod=1e9+7,inv2=5e8+4;
int n,lim,ans;
int a[MAX];
char s[MAX];
void FWT(int *A,int tt=1)
{
	for(int i=1;i<lim;i<<=1)
	  for(int l=i<<1,j=0;j<lim;j+=l)
	    for(int k=0,x,y;k<i;++k)
	      {
	      	x=A[j+k],y=A[i+j+k],A[j+k]=(x+y)%mod,A[i+j+k]=(x-y+mod)%mod;
	      	if(tt==-1) A[j+k]=1ll*A[j+k]*inv2%mod,A[i+j+k]=1ll*A[i+j+k]*inv2%mod;
		  }
}
int GET_NUM(int x)
{
	int num=0;
	while(x) x&=x-1,++num;
	return num;
}
int main()
{
	scanf("%d%s",&n,s),lim=1<<n;
	for(int i=0;i<lim;++i)
	  a[i]=s[i]-'0'; 
	FWT(a);
	for(int i=0;i<=lim;++i)
	  a[i]=1ll*a[i]*a[i]%mod;
	FWT(a,-1);
	for(int i=0;i<=lim;++i)
	  if(a[i]) (ans+=1ll*a[i]*(1<<(n-GET_NUM(i)))%mod)%=mod;
	return printf("%d",3ll*ans%mod),0;
}
```

