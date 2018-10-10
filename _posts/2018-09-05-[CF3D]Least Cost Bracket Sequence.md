---
layout:     post
title:      "[CF3D]Least Cost Bracket Sequence"
date:       2018-09-05
author:     "Dispwnl"
header-img: "img/used/35746.jpg"
catalog: true
tags:
    - 贪心
    - 单调队列
---
## [题目](http://codeforces.com/problemset/problem/3/D)
### 题意大意
给定一个字符串，字符串中有<code>'?','(',')'</code>，<code>'?'</code>可以替换成<code>'('</code>或<code>')'</code>，每个<code>'?'</code>替换都有不同的代价，问是否能构成一个合法的括号序列并是代价最小

### 输入输出样例
#### 输入样例#1： 
```
(??)
1 2
2 8
```
#### 输出样例#1： 
```
4
()()
```
### 题解
考虑贪心的思路，先把所有的<code>'?'</code>设成<code>')'</code>，把所有的<code>'('</code>设为$+1$，<code>')'</code>设为$-1$

如果前缀和小于0，说明缺少<code>'('</code>，把前面的某个<code>'?'</code>变成<code>'('</code>即可

选择<code>'?'</code>肯定不能乱选，根据贪心的思想，要选<code>')'</code>代价-<code>'('</code>代价最大的<code>'?'</code>，可以用单调队列维护，不匹配的情况就是需要<code>'?'</code>的时候队列为空或者到最后前缀和不为0

### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<queue>
# include<algorithm>
# define LL long long
using namespace std;
const int MAX=5e4+1;
struct p{
	int x,id;
	bool operator< (const p&a)
	const{
		return x<a.x;
	}
};
int L,sum,n;
LL ans;
string a;
priority_queue<p> qu;
int main()
{
	cin>>a;
	n=a.length();
	for(int i=0,a1,a2;i<n;++i)
	  {
	  	if(a[i]=='(') ++sum;
	  	else if(a[i]==')') --sum;
	  	else
	  	{
	  		scanf("%d%d",&a1,&a2),--sum,a[i]=')',ans+=a2;
	  		qu.push((p){a2-a1,i});
		}
		if(sum<0)
		{
			if(qu.empty()) return printf("-1"),0;
			p tt=qu.top();
			qu.pop();
			ans-=tt.x,a[tt.id]='(',sum+=2;
		}
	  }
	if(sum<0||sum>0) return printf("-1"),0;
	return cout<<ans<<endl<<a,0;
}
```

