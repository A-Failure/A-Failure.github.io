---
layout:         post
title:          "[TOPCODER14676]OwaskiAndTree"
date:           2018-10-16
author:         "Dispwnl"
header-img:     "img/used/45784.jpg"
catalog:    true
tags:
    - 动态规划
---
## [题目](https://vjudge.net/problem/TopCoder-14676)
### 题目大意
给出一棵有根树，从根出发，走过一些节点

每个节点有一个得分，可以重复经过节点，但是只有第一次经过会改变当前得分

如果当前得分为负，会马上变成$0$

求最大得分
### Examples
#### 0)	
```plain
{0, 1, 2, 3, 4, 5, 6, 7, 8}
{1, 1, -1, -1, -1, -1, 1, 1, 1, 1}
```
#### Returns:
```plain
4
```
#### 1)	
```plain
{0, 0, 1, 2}
{2, 3, 4, -1, -1}
```
#### Returns:
```plain
9
```
#### 2)	
```plain
{0, 0, 1, 1, 2, 2, 5, 5}
{1, 2, -3, -7, 3, 2, 7, -1, 3}
```
#### Returns:
```plain
17
```
#### 3)	
```plain
{0, 1, 1, 1, 0, 3, 1, 3, 4, 4, 3, 6, 8, 0, 12, 12, 11, 7, 7}
{-154011, 249645, 387572, 292156, -798388, 560085, -261135, -812756, 191481, -165204, 81513, -448791, 608073, 354614, -455750, 325999, 227225, -696501, 904692, -297238}
```
#### Returns:
```plain
3672275
```
#### 4)	
```plain
{}
{-1}
```
#### Returns:
```plain
0
```
### 题解
>$dp$题全都没思路完蛋了qaq

因为可以重复走并且每个点只能拿一次，要是忽略得分为负就变为$0$的条件就是要找包含根的树上最大权连通子图，即$f_i$表示选$val_i$的子树最大权值，$f_i=max(0,val_i+\sum_{j\in son_i}max(0,f_j))$

考虑变成$0$的情况，如果到某个节点权值变为$0$，可以看成前面遍历的节点都没有选

所以用$g_i$表示可以不选$val_i$的子树最大权值，则$g_i=max(f_i,\sum_{j\in son}g_j)$

答案就是$g_0$了

### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<vector>
# include<algorithm>
using namespace std;
const int MAX=1e3+5;
struct p{
	int x,y;
}c[MAX];
int n,num;
int h[MAX],val[MAX],f[2][MAX];
void add(int x,int y)
{
	c[++num]=(p){h[x],y},h[x]=num;
}
void dfs(int x=0)
{
	f[0][x]=val[x];
	for(int i=h[x];i;i=c[i].x)
	  dfs(c[i].y),f[0][x]+=max(f[0][c[i].y],0),f[1][x]+=f[1][c[i].y];
	f[0][x]=max(f[0][x],0),f[1][x]=max(f[1][x],f[0][x]);
}
class OwaskiAndTree{
	public:
		int maximalScore(vector<int> A,vector<int> B)
		{
			int n=B.size();
			for(int i=1;i<n;++i)
			  add(A[i-1],i);
			for(int i=0;i<n;++i)
			  val[i]=B[i];
			return dfs(),f[1][0];
		}
};
```
