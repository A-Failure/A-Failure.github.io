---
layout:     post
title:      "[TOPCODER12924]TypoCoderDiv1"
date:       2018-10-15
author:     "Dispwnl"
header-img: "img/used/6236.jpg"
catalog:  true
tags:
    - 动态规划
---
## [题目](https://vjudge.net/problem/TopCoder-12924)
### 题目大意
一共有$n$场比赛，打一场比赛赢了$+d_i$<code>rating</code>，输了$-d_i$<code>rating</code>，如果<code>rating</code>**不小于**$2200$为$Div1$，否则为$Div2$

$Aufun$决定去炸鱼，因为ta的确有伟大的神力，ta可以决定每一场比赛的输赢，为了恐吓$Div2$选手，激怒$Div1$选手，ta想最大化他在组别间横跳的次数，为了炸鱼，如果不是第$n$场比赛，$Aufun$必须在打上$Div1$下一场重返$Div2$

求最大横跳次数

### Examples
#### 0)	
```
{100,200,100,1,1}
2000
```
#### Returns:
```
3
```
#### 1)	
```
{0,0,0,0,0}
2199
```
#### Returns:
```
0
```
#### 2)	
```
{90000,80000,70000,60000,50000,40000,30000,20000,10000}
0
```
#### Returns:
```
1
```
#### 3)
```
{1000000000,1000000000,10000,100000,2202,1}
1000
```
#### Returns:
```
4
```
#### 4)
```
{2048,1024,5012,256,128,64,32,16,8,4,2,1,0}
2199
```
#### Returns:
```
0
```
#### 5)
```
{61,666,512,229,618,419,757,217,458,883,23,932,547,679,565,767,513,798,870,31,379,294,929,892,173,127,796,353,913,115,802,803,948,592,959,127,501,319,140,694,851,189,924,590,790,3,669,541,342,272}
1223
```
#### Returns:
```
29
```
#### 6)
```
{34,64,43,14,58,30,2,16,90,58,35,55,46,24,14,73,96,13,9,42,64,36,89,42,42,64,52,68,53,76,52,54,23,88,32,52,28,96,70,32,26,3,23,78,47,23,54,30,86,32}
1328
```
#### Returns:
```
20
```
### 题解
>自闭一上午后下午接着自闭……

![](/img/fish.gif)
$d_i$可能很大所以用<code>map</code>存

分类讨论一下就行了

课件上说<code>rating</code>大于$2200$才是$Div1$然后卡了一个多小时……

提交不上去又一个多小时……

![](/img/346.jpg)
### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<vector>
# include<map>
using namespace std;
map<int,int> f[51];
map<int,int>::iterator A;
class TypoCoderDiv1{
	public:
		int getmax(vector<int> d,int X)
		{
			int n=d.size(),ans=0;
			f[0][X]=0;
			for(int i=1;i<=n;++i)
			  for(A=f[i-1].begin();A!=f[i-1].end();++A)
			    {
			    	int D=(*A).first,tt=max(0,D-d[i-1]),LA=(*A).second;
			    	if(tt<2200) f[i][tt]=max(f[i][tt],LA+(D>=2200));
			    	if(D+d[i-1]<2200) f[i][D+d[i-1]]=max(f[i][D+d[i-1]],LA);
			    	else if(D<2200)
					{
						if(i==n) f[i][D+d[i-1]]=max(f[i][D+d[i-1]],LA+1);
						else if(D+d[i-1]-d[i]<2200) f[i][D+d[i-1]]=max(f[i][D+d[i-1]],LA+1),f[i+1][max(0,D+d[i-1]-d[i])]=max(f[i+1][max(0,D+d[i-1]-d[i])],LA+2);
					}
				}
			for(map<int,int>::iterator A=f[n].begin();A!=f[n].end();++A)
			  ans=max(ans,(*A).second);
			return ans;
		}
};
```
