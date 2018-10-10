---
layout:     post
title:      "[CF1025X]Codeforces Round #505 (rated, Div. 1 + Div. 2, based on VK Cup 2018 Final)"
date:       2018-09-10
author:     "Dispwnl"
header-img: "img/used/333.jpg"
catalog: true
tags:
    - 比赛
---
### [A　　Doggo Recoloring](http://codeforces.com/contest/1025/problem/A)
#### 题目大意
给定一个由小写字母组成的序列，每次可以把数量不小于$2$的字母变成其他任意字母，问是否可以将整个序列只包含一种字母

#### 题解
看是否有数量不小于$2$的字母就行了，注意特判只有一个字母的情况

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
int n;
int cnt[201];
string a;
int main()
{
	cin>>n>>a;
	if(n==1) return printf("Yes"),0;
	for(int i=0;i<n;++i)
	  ++cnt[a[i]];
	for(int i='a';i<='z';++i)
	  if(cnt[i]>=2) return printf("Yes"),0;
	return printf("No"),0;
}
```
### [B　　Weakened Common Divisor](http://codeforces.com/contest/1025/problem/B)
#### 题目大意
给你许多对数，要求找到一个数，使得ta能整除每一对中至少一个

#### 题解
找出第一对数所有的质因子，然后一个一个试就行了…

因为范围是2筛不出来，所以可以前$\sqrt{a}$用筛法筛，后面的直接枚举找

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<cmath>
# include<algorithm>
using namespace std;
const int MAX=2e5+1;
int n,tot,Tot;
int a[MAX],b[MAX],pm[MAX],Pm[MAX];
bool use[MAX];
bool ss(int x)
{
	if(x==2) return 1;
	int m=sqrt(x);
	for(int i=2;i<=m;++i)
	  if(x%i==0) return 0;
	return 1;
}
int main()
{
	scanf("%d",&n);
	for(int i=1;i<=n;++i)
	  scanf("%d%d",&a[i],&b[i]);
	int A1,B1,Gcd=max(A1=sqrt(a[1]),B1=sqrt(b[1]));
	for(int i=2;i<=Gcd;++i)
	  {
	  	if(!use[i])
	  	{
	  		pm[++tot]=i;
	  		if(a[1]%i==0||b[1]%i==0) Pm[++Tot]=i; 
		}
	  	for(int j=1;j<=tot&&pm[j]*i<=Gcd;++j)
	  	  {
	  	  	use[i*pm[j]]=1;
	  	  	if(i%pm[j]==0) break;
		  }
	  }
	int Num=Tot;
	for(int i=2;i<=A1;++i)
	  if(a[1]%i==0&&ss(a[1]/i)) Pm[++Tot]=a[1]/i;
	for(int i=2;i<=B1;++i)
	  if(b[1]%i==0&&ss(b[1]/i)) Pm[++Tot]=b[1]/i;
	if(ss(a[1])) Pm[++Tot]=a[1];
	if(ss(b[1])) Pm[++Tot]=b[1];
	for(int i=1;i<=Tot;++i)
	  {
	  	bool fl=0;
	  	for(int j=2;j<=n;++j)
	      if(a[j]%Pm[i]&&b[j]%Pm[i])
	      {
	      	fl=1;
	      	break;
		  }
		if(!fl) return printf("%d",Pm[i]),0;
	  }
	return printf("-1"),0;
}
```

### [C　　Plasticine zebra](http://codeforces.com/contest/1025/problem/C)
#### 题目大意
给定一个只包含<code>'b','w'</code>的字符串，每次可以选一个位置把字符串分成两段，每段前后翻转再合并，问能构成的最长的交替字符段是多长

#### 题解
发现如果有答案比不操作更优，答案肯定是字符串首尾接合最长交替字符段

所以把字符串看成一个环求最长答案就可以了

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
const int MAX=2e5+4;
char a[MAX];
int main()
{
	scanf("%s",a+1);
	int n=strlen(a+1),ans=0,L=0;
	for(int i=n+1;i<=n*2;++i)
	  a[i]=a[i-n];
	for(int i=1;i<=n*2;++i)
	  if(a[i]!=a[i-1]) ++L;
	  else ans=max(ans,L),L=1;
	ans=max(ans,L);
	if(ans>n) return printf("%d",n),0;
	return printf("%d",ans),0;
}
```
### [D　　Recovering BST](http://codeforces.com/contest/1025/problem/D)
#### 题目大意
给定一堆从大到小的值，问是否可以构成一棵二叉搜索树，并保证一条边相连的两个值$\gcd>1$

#### 题解
首先可以把值与值之间的关系处理出来

考虑区间$dp$，设$f_{i,j}$表示以$i-1$为根时区间$i,j$是否能形成一棵二叉搜索树，$g_{i,j}$表示以$j+1$为根时区间$i,j$是否能形成一棵二叉搜索树

最后枚举每个点作为根时是否满足条件即可

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
const int MAX=702;
int n;
int a[MAX];
int f[2][MAX][MAX];
bool b[MAX][MAX];
int gcd(int a,int b)
{
	return b?gcd(b,a%b):a;
}
int main()
{
	scanf("%d",&n);
	for(int i=1;i<=n;++i)
	  scanf("%d",&a[i]);
	for(int i=1;i<=n;++i)
	  for(int j=i+1;j<=n;++j)
		if(gcd(a[i],a[j])>1) b[i][j]=b[j][i]=1;
	for(int i=1;i<=n;++i)
	  b[0][i]=b[n+1][i]=b[i][0]=b[i][n+1]=1;
	for(int i=n;i>=1;--i)
	  for(int j=i;j<=n;++j)
	    for(int k=i;k<=j;++k)
	      if((k==i||f[0][i][k-1])&&(k==j||f[1][k+1][j])) f[1][i][j]|=b[k][i-1],f[0][i][j]|=b[k][j+1];
	for(int i=1;i<=n;++i)
	  if((i==1||f[0][1][i-1])&&(i==n||f[1][i+1][n])) return printf("Yes"),0;
	return printf("No"),0;
}
```
### [E　　Colored Cubes](http://codeforces.com/contest/1025/problem/E)
#### 题目大意
给定一个$n*n$的棋盘，上面有$m$个方块，每个位置每个时刻最多存在一个方块，每次只能移动一个方块并且只能像上下左右移动一格

方块$i$开始在$(x_i,y_i)$，要求移动到$(x_i',y_i')$，问能否在$10800$次移动内完成任务

#### 题解
发现不是要求最小步数？

将移动到最终位置看成有一个方块从最终位置开始移动，然后对应的两个方块在某一点相碰

可以将方块$i$移动到$(i,i)$，将最终位置的也移到$(i,i)$，好像可以[证明这种移法是不会超出范围的](http://codeforces.com/blog/entry/61323)，但是我不会…

可以将方块按行排序，然后移到排序后的$i$行，这时候肯定每行只有一个方块；然后将每个方块移到指定列，这时候肯定每列只有一个方块，再将每个方块移到指定行，最终位置同理

#### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
const int MAX=51,MAXN=11001;
struct p{
	int x,y,id;
	bool operator< (const p &a)
	const{
		return x<a.x;
	}
}a[MAX];
struct o{
	int x,y,_x,_y;
}ans[MAXN];
int n,m,num,num1;
int main()
{
	scanf("%d%d",&n,&m);
	for(int i=1;i<=m;++i)
	  scanf("%d%d",&a[i].x,&a[i].y),a[i].id=i;
	sort(a+1,a+1+m);
	for(int i=1;i<=m;++i)
	  if(a[i].x>i) for(;a[i].x>i;--a[i].x)
	    ans[++num]=(o){a[i].x,a[i].y,a[i].x-1,a[i].y};
	for(int i=m;i>=1;--i)
	  for(;a[i].x<i;++a[i].x)
	    ans[++num]=(o){a[i].x,a[i].y,a[i].x+1,a[i].y};
	for(int i=1;i<=m;++i)
	  {
	  	for(;a[i].y>a[i].id;--a[i].y)
	  	  ans[++num]=(o){a[i].x,a[i].y,a[i].x,a[i].y-1};
	  	for(;a[i].y<a[i].id;++a[i].y)
	  	  ans[++num]=(o){a[i].x,a[i].y,a[i].x,a[i].y+1};
	  }
	for(int i=1;i<=m;++i)
	  {
	  	for(;a[i].x>a[i].id;--a[i].x)
	  	  ans[++num]=(o){a[i].x,a[i].y,a[i].x-1,a[i].y};
	  	for(;a[i].x<a[i].id;++a[i].x)
	  	  ans[++num]=(o){a[i].x,a[i].y,a[i].x+1,a[i].y};
	  }
	num1=num;
	for(int i=1;i<=m;++i)
	  scanf("%d%d",&a[i].x,&a[i].y),a[i].id=i;
	sort(a+1,a+1+m);
	for(int i=1;i<=m;++i)
	  if(a[i].x>i) for(;a[i].x>i;--a[i].x)
	    ans[++num1]=(o){a[i].x,a[i].y,a[i].x-1,a[i].y};
	for(int i=m;i>=1;--i)
	  for(;a[i].x<i;++a[i].x)
	    ans[++num1]=(o){a[i].x,a[i].y,a[i].x+1,a[i].y};
	for(int i=1;i<=m;++i)
	  {
	  	for(;a[i].y>a[i].id;--a[i].y)
	  	  ans[++num1]=(o){a[i].x,a[i].y,a[i].x,a[i].y-1};
	  	for(;a[i].y<a[i].id;++a[i].y)
	  	  ans[++num1]=(o){a[i].x,a[i].y,a[i].x,a[i].y+1};
	  }
	for(int i=1;i<=m;++i)
	  {
	  	for(;a[i].x>a[i].id;--a[i].x)
	  	  ans[++num1]=(o){a[i].x,a[i].y,a[i].x-1,a[i].y};
	  	for(;a[i].x<a[i].id;++a[i].x)
	  	  ans[++num1]=(o){a[i].x,a[i].y,a[i].x+1,a[i].y};
	  }
	printf("%d\n",num1);
	for(int i=1;i<=num;++i)
	  printf("%d %d %d %d\n",ans[i].x,ans[i].y,ans[i]._x,ans[i]._y);
	for(int i=num1;i>num;--i)
	  printf("%d %d %d %d\n",ans[i]._x,ans[i]._y,ans[i].x,ans[i].y);
	return 0;
}
```
