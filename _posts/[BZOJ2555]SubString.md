---
layout:     post
title:      "[BZOJ2555]SubString"
date:       2019-01-07
author:     "Dispwnl"
header-img: "img/used/3145.jpg"
catalog: false
tags:
    - 后缀自动机
    - Link Cut Tree
---
## [题目](https://lydsy.com/JudgeOnline/problem.php?id=2555)
### Description
懒得写背景了，给你一个字符串init，要求你支持两个操作

(1):在当前字符串的后面插入一个字符串

(2):询问字符串s在当前字符串中出现了几次？(作为连续子串)

你必须在线支持这些操作。

### Input
第一行一个数Q表示操作个数

第二行一个字符串表示初始字符串init

接下来Q行，每行2个字符串Type,Str

Type是ADD的话表示在后面插入字符串。

Type是QUERY的话表示询问某字符串在当前字符串
中出现了几次。

为了体现在线操作，你需要维护一个变量mask，初
始值为0 
![](https://lydsy.com/JudgeOnline/upload/201112/11.JPG)

读入串Str之后，使用这个过程将之解码成真正询问的串TrueStr。

询问的时候，对TrueStr询问后输出一行答案Result

然后mask=maskxorResult

插入的时候，将TrueStr插到当前字符串后面即可。

HINT:ADD和QUERY操作的字符串都需要解压

长度 <= 600000，询问次数<= 10000,询问总长度<= 3000000

### Output
#### Sample Input
```
2
A
QUERY B
ADD BBABBBBAAB
```
#### Sample Output
```
0
```
### 题解
如果没有加入操作，每次询问就是询问$SAM$上一个点的$right$大小，就是后缀树上的子树权值和

有了加入操作后，后缀树不断变化，所以可以用$LCT$维护了

### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
const int MAX=1.2e6+5;
int n,top,Q;
int st[MAX],siz[MAX];
char A[10],a[MAX],b[MAX*3];
struct SAM{
	int l,r,L;
	int len[MAX],fa[MAX];
	int son[MAX][26];
	SAM() {l=r=1;}
	struct Link_Cut_Tree{
		int s[MAX],_s[MAX],fa[MAX];
		int son[MAX][2];
		bool fl[MAX];
		void pus(int x) {s[x]=s[son[x][0]]+s[son[x][1]]+_s[x]+siz[x];}
		void down(int x)
		{
			if(fl[x]&&x)
			{
				if(son[x][0]) fl[son[x][0]]^=1;
				if(son[x][1]) fl[son[x][1]]^=1;
				fl[x]=0,swap(son[x][0],son[x][1]);
			}
		}
		bool GET_ID(int x) {return son[fa[x]][1]==x;}
		bool is_root(int x) {return son[fa[x]][0]!=x&&son[fa[x]][1]!=x;}
		void rot(int x)
		{
			int y=fa[x],z=fa[y],k=GET_ID(x);
			if(!is_root(y)) son[z][GET_ID(y)]=x;
			son[y][k]=son[x][k^1],son[x][k^1]=y,fa[son[y][k]]=y,fa[y]=x,fa[x]=z;
			pus(y),pus(x);
		}
		void splay(int x)
		{
			int qwq=x;
			for(;!is_root(qwq);qwq=fa[qwq])
			  st[++top]=qwq;
			down(qwq);
			while(top) down(st[top--]);
			for(int y;!is_root(x);rot(x))
			  if(!is_root(y=fa[x])) rot(GET_ID(x)==GET_ID(y)?y:x);
		}
		void access(int x)
		{
			for(int y=0;x;y=x,x=fa[x])
			  splay(x),_s[x]+=s[son[x][1]],_s[x]-=s[son[x][1]=y],pus(x);
		}
		void make_root(int x) {access(x),splay(x),fl[x]^=1;}
		void split(int x,int y) {make_root(x),access(y),splay(y);}
		void link(int x,int y) {split(x,y),_s[fa[x]=y]+=s[x],pus(y);}
		void cut(int x,int y)
		{
			split(x,y);
			if(son[y][0]==x) fa[x]=son[y][0]=0;
			pus(y);
		}
	}Tree;
	void ins(int x)
	{
		int tt=r;
		len[r=++l]=++L,siz[r]=1;
		for(;tt&&!son[tt][x];tt=fa[tt])
		  son[tt][x]=r;
		if(!tt)
		{
			Tree.link(r,1);
			return void(fa[r]=1);
		}
		int qwq=son[tt][x];
		if(len[qwq]==len[tt]+1)
		{
			Tree.link(r,qwq);
			return void(fa[r]=qwq);
		}
		len[++l]=len[tt]+1,Tree.cut(qwq,fa[qwq]),Tree.link(l,fa[qwq]),Tree.link(qwq,l),Tree.link(r,l),fa[l]=fa[qwq],fa[qwq]=fa[r]=l;
		for(int i=0;i<26;++i)
		  son[l][i]=son[qwq][i];
		for(int i=tt;son[i][x]==qwq;i=fa[i])
		  son[i][x]=l;
	}
	int Solve()
	{
		int _n=strlen(b),x=1;
		for(int i=0;i<_n;++i)
		  {
		  	x=son[x][b[i]-'A'];
		  	if(!x) return 0;
		  }
		return Tree.split(1,x),Tree.s[x]-Tree.s[Tree.son[x][0]];
	}
}_S;
void GET_TRUE(int _n,int mask)
{
	for(int i=0;i<_n;++i)
	  mask=(mask*131+i)%_n,swap(b[i],b[mask]);
}
int main()
{
	scanf("%d%s",&Q,a+1),n=strlen(a+1);
	for(int i=1;i<=n;++i)
	  _S.ins(a[i]-'A');
	for(int i=1,mask=0,ans,_n;i<=Q;++i)
	  {
	  	scanf("%s%s",A,b),_n=strlen(b),GET_TRUE(_n,mask);
	  	if(A[0]=='Q') printf("%d\n",ans=_S.Solve()),mask^=ans;
	  	else if(A[0]=='A') for(int j=0;j<_n;++j)
	  	  _S.ins(b[j]-'A');
	  }
	return 0;
}
```
