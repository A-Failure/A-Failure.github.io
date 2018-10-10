---
layout:     post
title:      "[CF1036X]Educational Codeforces Round 50 (Rated for Div. 2)"
date:       2018-09-08
author:     "Dispwnl"
header-img: "img/used/566.jpg"
catalog: true
tags:
    - 比赛
---
> edu场上分啦233

### [A　　Function Height](http://codeforces.com/contest/1036/problem/A)
#### 题目大意
给你$(0,0)$到$(2n,0)$这$2n+1$个点，其中奇数点的$y$可以调整

将点与下一个点之间连线，要使与$x$轴构成的图形面积正好为$k$，求点中最大的$y$的最小值

#### 题解
构成了许多三角形，每个的面积为$y$值，所以答案为$\lceil\frac{k}{n}\rceil$（不足面积就整体$+1$，超过面积不用管，因为可以把部分$y$变小）

#### 代码
```
# include<bits/stdc++.h>
# define LL long long
using namespace std;
LL n,k;
int main()
{
	cin>>n>>k;
	LL ans=k/n;
	if(ans*n<k) ++ans;
	cout<<ans;
	return 0;
}
```

### [B　　Diagonal Walking v.2](http://codeforces.com/contest/1036/problem/B)
#### 题目大意
一个点，初始在$(0,0)$，每次可以直着或者斜着走一步，要求正好$k$步走到给定点，问斜着走的最大步数是多少

#### 题解
首先$k<max(n,m)$是绝对走不到目标点的

先考虑$n=m$的对角线做法
![](/img/study/bxiazou.png)


- 假设$n$和$k$奇偶性相同，直接走对角线就是最优的方法且一定能走到目标点，因为多了的部分可以通过折返走消掉
- 假设$n$和$k$奇偶性不同，可以通过先走到$(1,1)$来变成奇偶性相同的情况，如图
![](/img/study/bxiazou1.png)

若$n!=m$，我们可以先向$n,m$中比较大的方向走一段，然后就构成了$n=m$的对角线情况

还是分$n,m$的奇偶性考虑，设$x=max(n,m)-min(n,m)$

- 若$n,m$的奇偶性相同，$x$肯定是偶数，所以两个格子分解成两个斜步走就行了
- 若$n,m$的奇偶性不同，$x$肯定为奇数，所以前面两个格子走两个斜步，最后一个格子走直步就行了

#### 代码
```
# include<bits/stdc++.h>
# define LL long long
using namespace std;
LL q,k,n,m;
int main()
{
	cin>>q;
	while(q--)
	{
		cin>>n>>m>>k;
		if(k<max(n,m)) cout<<-1<<endl;
		else if(n%2!=m%2) cout<<k-1<<endl;
		else
		{
			if((k-min(n,m))%2==0) cout<<k<<endl;
			else cout<<k-2<<endl;
		}
	}
	return 0;
}
```
### [C　　Classy Numbers](https://codeforces.com/problemset/problem/1036/C)
#### 题目大意
给你一个范围$[L,R]$，求这个范围里每一位非零数字的个数和不大于3的数字个数

#### 题解
一个简单的数位$dp$，结果为$ans_r-ans_{l-1}$

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
# define LL long long
using namespace std;
int T,num;
LL L,R,Ans1,Ans2;
LL ln[19];
LL f[19][4];
void GET_POS(LL x)
{
	while(x) ln[++num]=x%10,x/=10;
}
LL dfs(int l=num,int r=0,bool fl=1)
{
	if(r>3) return 0;
	if(!l) return 1;
	if(!fl&&f[l][r]!=-1) return f[l][r];
	int lim=fl?ln[l]:9;
	LL Ans=0;
	for(int i=0;i<=lim;++i)
	  Ans+=dfs(l-1,r+(i!=0),fl&&i==ln[l]);
	if(!fl) f[l][r]=Ans;
	return Ans;
}
int main()
{
	scanf("%d",&T);
	while(T--)
	{
		cin>>L>>R;
		memset(f,-1,sizeof(f));
		num=0,GET_POS(R),Ans1=dfs();
		num=0,GET_POS(L-1),Ans2=dfs();
		cout<<Ans1-Ans2<<endl;
	}
	return 0;
}
```

### [D　　Vasya and Arrays](https://codeforces.com/contest/1036/problem/D)
#### 题目大意
给你两个序列，每次可以选择一个序列的连续一部分合并，若不可以使两个序列相同输出$-1$，否则输出满足条件的情况下最后序列的最长长度

#### 题解
>开始用$cin$读入T了一发...

发现可以贪心，搞两个指针，同时维护两个序列的前缀和，每次比较两个前缀和，哪个小哪个指针就往后移，相同就两个指针同时向后移，最后看两个序列长度是否相同就好了

#### 代码
```
# include<bits/stdc++.h>
# define LL long long
using namespace std;
const int MAX=3e5+1;
LL n,m,sum1,sum2;
LL a[MAX],b[MAX];
int main()
{
	scanf("%I64d",&n);
	for(int i=1;i<=n;++i)
	  scanf("%I64d",&a[i]);
	scanf("%I64d",&m);
	for(int i=1;i<=m;++i)
	  scanf("%I64d",&b[i]);
	int l=1,r=1,N=n,M=m;
	sum1=a[1],sum2=b[1];
	while(l<=n&&r<=m)
	{
		if(sum1<sum2) sum1+=a[++l],--N;
		else if(sum1>sum2) sum2+=b[++r],--M;
		else sum1+=a[++l],sum2+=b[++r];
	}
	if(N!=M) cout<<-1;
	else cout<<N;
	return 0;
}
```
