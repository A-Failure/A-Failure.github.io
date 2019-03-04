---

layout:     post
title:      "[CF1038X]Codeforces Round #508 (Div. 2)"
date:       2018-09-07
author:     "Dispwnl"
header-img: "img/used/8897.jpg"
catalog: true
tags:
    - 比赛
---
> 掉分掉的太惨了QAQ

> 整场比赛的基调![](/img/study/gg.png)

### [A　　Equality](http://codeforces.com/problemset/problem/1040/A)
#### 题目大意
给定一个长度为$n$的字符串和$k$，问这个字符串包含字母表前$k$个大写字母并且数量相同的子序列最长是多少

#### 题解
找整个字符串出现次数最少的字符个数再$\times k$即可

#### 代码
```
# include<bits/stdc++.h>
# define LL long long
using namespace std;
int n,k;
string a;
int cnt[201];
int main()
{
	cin>>n>>k>>a;
	for(int i=0;i<n;++i)
	  ++cnt[a[i]];
	int minn=1e9;
	for(int i='A';i<'A'+k;++i)
	  minn=min(minn,cnt[i]);
	return printf("%d",minn*k),0;
}
```

### [B　　Non-Coprime Partition](http://codeforces.com/problemset/problem/1038/B)
#### 题目大意
给定一个整数$n$，要求把$1$到$n$分别放入两个序列$s1,s2$，使得$\gcd(sum(s1),sum(s2))>1$

#### 题解
首先$n=1,2$的情况肯定无解

考虑通过奇偶分开，发现这样两个序列为等差数列，个数分别为$num1$和$num2$，$sum$分别为$num1+\frac{2num1(num1-1)}{2}=num1^2$和$2num2+\frac{2num2(num2-1)}{2}=num2(num2+1)$ ，分$n$的奇偶性讨论一下：

- $n$为奇数，$num1=num2+1$，所以有公因数$num2+1$
- $n$为偶数，$num1=num2$，所以有公因数$num2$

可见无论$n$为奇偶，$\gcd$都不为$1$，所以这样做是可行的

#### 代码
```
# include<bits/stdc++.h>
# define LL long long
using namespace std;
const int MAX=5e4+1;
int n,num1,num2;
int a[MAX],b[MAX];
int main()
{
	cin>>n;
	if(n<=2) return printf("No"),0;
	for(int i=1;i<=n;++i)
	  if(i%2) a[++num1]=i;
	  else b[++num2]=i;
	cout<<"Yes"<<endl<<num1<<" ";
	for(int i=1;i<=num1;++i)
	  cout<<a[i]<<" ";
	cout<<endl;
	cout<<num2<<" ";
	for(int i=1;i<=num2;++i)
	  cout<<b[i]<<" ";
	return 0;
}
```

### [C　　Gambling](http://codeforces.com/problemset/problem/1038/C)
#### 题目大意
两个人$A,B$玩游戏，每个人有1个长度为$n$的序列，每次一个人可以从序列中拿一个数并加入自己的分数，或者把对手序列中没选的数中去掉一个，这两个人都足够聪明，求$A$分数与$B$分数的差

#### 题解
贪心，去除对手一个数可以看成自己分数加上那个数，所以每个人选的时候比较自己序列中的最大的数和对手序列中最大的数哪个大

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<queue>
# include<algorithm>
# define LL long long
using namespace std;
const int MAX=1e5+1;
int n;
LL ans;
int a[MAX],b[MAX];
priority_queue<int> qu1,qu2;
int main()
{
	scanf("%d",&n);
	for(int i=1;i<=n;++i)
	  scanf("%d",&a[i]),qu1.push(a[i]);
	for(int i=1;i<=n;++i)
	  scanf("%d",&b[i]),qu2.push(b[i]);
	for(int i=1;i<=n;++i)
	  {
	  	if(qu1.empty()) qu2.pop();
	  	else if(qu2.empty()) ans+=qu1.top(),qu1.pop();
	  	else
	  	{
	  		if(qu1.top()>qu2.top()) ans+=qu1.top(),qu1.pop();
	  		else qu2.pop();
		}
		if(qu2.empty()) qu1.pop();
	  	else if(qu1.empty()) ans-=qu2.top(),qu2.pop();
	  	else
	  	{
	  		if(qu2.top()>qu1.top()) ans-=qu2.top(),qu2.pop();
	  		else qu1.pop();
		}
	  }
	return cout<<ans,0;
}
```

### [D　　Slime](http://codeforces.com/problemset/problem/1038/D)
#### 题目大意
有$n$只史莱姆，每只史莱姆有一个分数，每次一只史莱姆可以吞掉左边的或者右边的史莱姆（要是有的话），然后ta的分数会减去被吞的史莱姆的分数，问最后剩下的史莱姆分数最大为多少

#### 题解
发现每个分数结果中只有正负性变化，所以考虑不同情况符号该怎么分配

- 如果全是正数，肯定要有一个数变成负数，然后求和，所以最大分数就是总和减去最小的正数
- 如果全是负数，肯定要有一个数不变，其他数取正，然后求和，所以最大分数就是绝对值总和加上最大的负数
- 如果有正有负，可以综上考虑，正数可以被负数同化成负数，而负数情况中不变的数可以随意选一个正数，所以最大分数就是所有史莱姆绝对值的和

比赛时忘加了负数情况然后漂亮的$fst$了QAQ

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdlib>
# include<cstdio>
# include<algorithm>
# define LL long long
using namespace std;
const int MAX=5e5+1;
int n,minn=1e9,maxn=-1e9;
LL ans;
int a[MAX];
int main()
{
	scanf("%d",&n);
	bool fl=0,fl1=0;
	for(int i=1;i<=n;++i)
	  {
	  	scanf("%d",&a[i]),ans+=abs(a[i]);
	  	if(a[i]<0) fl=1,maxn=max(maxn,a[i]);
	  	else fl1=1,minn=min(minn,a[i]);
	  }
	if(n==1) return printf("%d",a[1]),0;
	if(!fl) return cout<<ans-2*minn,0;
	if(!fl1) return cout<<ans+2*maxn,0;
	if(fl&&fl1) return cout<<ans,0;
}
```
