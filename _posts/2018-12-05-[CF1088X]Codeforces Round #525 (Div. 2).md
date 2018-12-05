---
layout:         post
title:          "[CF1088X]Codeforces Round #525 (Div. 2) "
date:           2018-12-05
auther:         "Dispwnl"
header-img:     "img/used/534.jpg"
catalog:        true
tags:
    - 比赛
---
> 感觉这场CF有点不够$Div2$难度啊……没打真是可惜了

> 恭喜$XG$大爷一场上紫！
### [A　　Ehab and another construction problem](http://codeforces.com/contest/1088/problem/A)
#### 题目大意
找两个数$a,b$，使得

- $1\le a,b\le x$
- $b$能整除$a$
- $a·b>x$
- $\frac{a}{b}<x$

#### 题解
枚举即可

#### 代码
```
# include<bits/stdc++.h>
using namespace std;
int x;
int main()
{
	scanf("%d",&x);
	for(int i=1;i<=x;++i)
	  for(int j=i;j<=x;j+=i)
	    if(i*j>x&&j/i<x) return printf("%d %d",j,i),0;
	return printf("-1"),0;
}
```

### [B　　Ehab and subtraction](http://codeforces.com/contest/1088/problem/B)
#### 题目大意
给定一个序列$a$，每次找到序列中最小的正整数（没有则为$0$），输出这个数并且序列中每个数都减去这个数，重复这样的操作$k$次

#### 题解
这个值在原序列中肯定是单调递增的，排下序记录每次的答案扫一遍即可

#### 代码
```
# include<bits/stdc++.h>
using namespace std;
const int MAX=1e5+5;
int n,k,ans;
int a[MAX];
int main()
{
	scanf("%d%d",&n,&k);
	for(int i=1;i<=n;++i)
	  scanf("%d",&a[i]);
	sort(a+1,a+1+n);
	for(int i=1,L=0;i<=k;++i)
	  {
	  	while(L<=n&&a[L]<=ans) ++L;
	  	if(L<=n) printf("%d\n",a[L]-ans),ans=a[L];
	  	else printf("0\n");
	  }
	return 0;
}
```

### [C　　Ehab and another construction problem](http://codeforces.com/contest/1088/problem/C)
#### 题目大意
给定一个长度为$n$的序列$a$，有两种操作：

- 选择前$i$个数同加$x(0\le x\le 10^6)$
- 选择前$i$个数同对$x(1\le x\le 10^6)$取模

求一种方案使得在使用不超过$n+1$次操作后序列单调递增

#### 题解
发现对$x$取模对于小于$x$的数是没有影响的

对于$a_i,a_{i+1}$，模数$x$需满足$a_i<x,a_{i+1}\% x>a_i$

所以先开始同加一个较大的数，每次对$a_i$取模$a_i-i+1$即可保证单调递增，操作数是$n$

#### 代码
```
# include<bits/stdc++.h>
using namespace std;
const int MAX=2e3+5;
int n;
int a[MAX];
int main()
{
	scanf("%d",&n);
	for(int i=1;i<=n;++i)
	  scanf("%d",&a[i]),a[i]+=2*n;
	printf("%d\n1 %d %d\n",n,n,2*n);
	for(int i=1;i<n;++i)
	  printf("2 %d %d\n",i,a[i]-i+1);
	return 0;
}
```

### [D　　Ehab and another construction problem](http://codeforces.com/contest/1088/problem/D)
#### 题目大意
交互题

现在有两个数$a,b$，每次可以询问两个数$c,d$

- 如果$a\oplus c>b\oplus d$，返回$1$
- 如果$a\oplus c=b\oplus d$，返回$0$
- 如果$a\oplus c<b\oplus d$，返回$-1$

要求你在少于$62$次操作中把$a,b$求出来

#### 题解
假设我们已经知道$a,b$第$k$位以后各个位是多少（即知道忽略第$k$位之前$a,b$的大小），现在要搞第$k-1$位

如果第$k$位以后的$a!=b$，对于第$k-1$位，如果查询$(c+2^{k-1},d+2^{k-1})$

- 如果返回$1$，说明$a_{k-1}=0$，$b_{k-1}$是多少可以通过查询$(c+2^{k-1},d)$得到
- 如果返回$0$，说明前面的$a=b$，可以通过查询$(c+2^{k-1},d)$得到$a_{k-1},b_{k-1}$具体值
- 如果返回$-1$，说明$b_{k-1}=0$，$a_{k-1}$是多少可以通过查询$(c+2^{k-1},d)$得到

初始的时候查询$(0,0)$得到$a,b$总的大小关系，然后一位位往前推即可

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
int query(int C,int D,int x=0)
{
	printf("? %d %d\n",C,D);
	fflush(stdout),scanf("%d",&x);
	return x;
}
int main()
{
	int c=0,d=0,a=0,b=0,fl=query(0,0);
	for(int i=29,w,qwq;i>=0;--i)
	  {
	  	w=(1<<i);
	  	if(!fl)
	  	{
	  		qwq=query(c+w,d);
	  		if(qwq==-1) a+=w,b+=w;
		}
		else if(fl==1)
		{
			fl=query(c+w,d+w);
			if(fl==-1) a+=w,c+=w,fl=query(c,d);
			else if((fl==1||!fl)&&query(c+w,d)==-1) a+=w,b+=w;
		}
		else if(fl==-1)
		{
			fl=query(c+w,d+w);
			if(fl==1) b+=w,d+=w,fl=query(c,d);
			else if((fl==-1||!fl)&&query(c+w,d)==-1) a+=w,b+=w;
		}
	  }
	return printf("! %d %d",a,b),0;
}
```

### [E　　Ehab and another construction problem](http://codeforces.com/contest/1088/problem/E)
#### 题目大意
给定一棵树，找出$k$（$k$不给定）个树上不重叠连通块$A$，使得$\frac{\sum_{i=1}^{k}A_i}{k}$最大

#### 题解
找到树上权值和最大的连通块，然后贪心找树上有多少个权值和等于这个值的连通块

完了

#### 代码
```
# include<bits/stdc++.h>
# define LL long long
using namespace std;
const int MAX=3e5+5;
LL inf=-1e18;
struct p{
	int x,y;
}c[MAX<<1];
int n,num,tot;
LL ans=inf;
int h[MAX],d[MAX],w[MAX];
void add(int x=0,int y=0)
{
	scanf("%d%d",&x,&y);
	c[++num]=(p){h[x],y},h[x]=num;
	c[++num]=(p){h[y],x},h[y]=num;
}
LL dfs(int x=1,int fa=0)
{
	LL sum=w[x];
	for(int i=h[x];i;i=c[i].x)
	  if(c[i].y^fa) sum+=max(0ll,dfs(c[i].y,x));
	return ans=max(ans,sum),sum;
}
LL dfs1(int x=1,int fa=0)
{
	LL sum=w[x];
	for(int i=h[x];i;i=c[i].x)
	  if(c[i].y^fa) sum+=max(0ll,dfs1(c[i].y,x));
	if(sum==ans) ++tot,sum=0;
	return sum;
}
int main()
{
	scanf("%d",&n);
	for(int i=1;i<=n;++i)
	  scanf("%d",&w[i]);
	for(int i=1;i<n;++i,add()); 
	return dfs(),dfs1(),printf("%I64d %d",ans*tot,tot),0; 
}
```

