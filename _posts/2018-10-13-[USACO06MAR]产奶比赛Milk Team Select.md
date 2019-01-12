---
layout:     post
title:      "[USACO06MAR]产奶比赛Milk Team Select"
date:       2018-10-13
author:     "Dispwnl"
header-img: "img/used/464.jpg"
catalog: true
tags:
    - 动态规划
---
## [题目](https://www.lydsy.com/JudgeOnline/problem.php?id=1722)
### Description
约翰的N(1≤N≤500)头奶牛打算组队去参加一个世界级的产奶比赛(Multistate Milking Match-up，缩写为MMM)．她们很清楚其他队的实力，也就是说，她们派出的队只要能产出至少X(I≤X≤1000000)加仑牛奶，就能赢得这场比赛．

每头牛都能为集体贡献一定量的牛奶，数值在-10000到10000之间（有些奶牛总是想弄翻装着其他奶牛产的奶的瓶子）．

MMM的举办目的之一，是通过竞赛中的合作来增进家庭成员之间的默契．奶牛们认为她们总是能赢得这场比赛，但为了表示对比赛精神的支持，她们希望在选出的队伍里能有尽可能多的牛来自同一个家庭，也就是说，有尽可能多对的牛有直系血缘关系（当然，这支队伍必须能产出至少X加仑牛奶）．当然了，所有的奶牛都是女性，所以队伍里所有直系血亲都是母女关系．

约翰熟知所有奶牛之间的血缘关系．现在他想知道，如果在保证一支队伍能赢得比赛的情况下，队伍中最多能存在多少对血缘关系．注意，如果一支队伍由某头奶牛和她的母亲、她的外祖母组成，那这支队伍里一共有2对血缘关系（这头奶牛外祖母与她的母亲，以及她与她的母亲）．

### Input
第1行：两个用空格隔开的整数N和X.

第2到N+1行：每行包括两个用空格隔开的整数，第一个数为一只奶牛能贡献出的牛奶的加仑数，第二个数表示她的母亲的编号．如果她的母亲不在整个牛群里，那第二个数为0．并且，血缘信息不会出现循环，也就是说一头奶牛不会是自己的母亲或祖母，或者更高代的祖先．

### Output
输出在一个能获胜的队伍中，最多可能存在的有血缘关系的牛的对数．如果任何一支队伍都不可能获胜，输出-1.

### Sample Input
```plain
5 8
-1 0
3 1
5 1
-3 3
2 0
```
### Sample Output
```plain
2
```
### HINT
约翰一共有5头奶牛．第1头奶牛能提供-1加仑的牛奶，且她是第2、第3头奶牛的母亲．第2、第3头奶牛的产奶量务别为3加仑和5加仑．第4头奶牛是第3头奶牛的女儿，她能提供-3加仑牛奶．还有与其他牛都没有关系的第5头奶牛，她的产奶量是2加仑．

最好的一支队伍包括第1，2，3，5头奶牛．她们一共能产出（-1）+3+5+2=9≥8加仑牛奶，

并且这支队伍里有2对牛有血缘关系（1—2和1-3）．如果只选第2，3，5头奶牛，虽然总产奶量会更高（10加仑），但这支队伍里包含的血缘关系的对数比上一种组合少（队伍里没有血缘关系对）．

### 题解

### 题解
设$f_{0/1,x,i}$表示以$x$为根的子树里选中了$i$对相邻点，$0$表示没选$x$，$1$表示选了$x$

然后树上背包即可

### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
const int MAX=505;
struct p{
	int x,y;
}c[MAX<<1];
int n,X,num;
int h[MAX],gl[MAX],siz[MAX];
int f[2][MAX][MAX];
int read()
{
	int x=0,fl=1;
	char ch=getchar();
	for(;!isdigit(ch);fl=(ch=='-')?-1:1,ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x*fl;
}
void add(int x,int fa)
{
	c[++num]=(p){h[fa],x},h[fa]=num;
}
void dfs(int x=0)
{
	siz[x]=1;
	for(int i=h[x];i;i=c[i].x)
	  dfs(c[i].y),siz[x]+=siz[c[i].y];
	for(int i=h[x];i;i=c[i].x)
	  for(int j=siz[x]-1;j>=0;--j)
	  	for(int k=0;k<=min(siz[c[i].y]-1,j);++k)
	      {
			f[0][x][j]=max(f[0][x][j],max(f[1][c[i].y][k],f[0][c[i].y][k])+f[0][x][j-k]);
			if(x)
			{
				f[1][x][j]=max(f[1][x][j],f[0][c[i].y][k]+f[1][x][j-k]);
			 	if(k!=j)  f[1][x][j]=max(f[1][x][j],f[1][c[i].y][k]+f[1][x][j-k-1]);
			}
		  }
	for(int i=0;i<siz[x];++i)
	  f[1][x][i]+=gl[x];
}
int main()
{
	n=read(),X=read();
	for(int i=0;i<=n;++i)
	  for(int j=0;j<=n;++j)
	    f[0][i][j]=f[1][i][j]=-1e9;
	for(int i=0;i<=n;++i)
	  f[0][i][0]=f[1][i][0]=0;
	for(int i=1;i<=n;++i)
	  gl[i]=read(),add(i,read());
	dfs();
	for(int i=n-1;i>=0;--i)
	  if(f[0][0][i]>=X) return printf("%d",i),0;
	return printf("-1"),0;
}
```
