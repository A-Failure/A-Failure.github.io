---
layout:     post
title:      "[SPOJ]8093 Sevenk Love Oimaster"
date:       2019-01-05
author:     "Dispwnl"
header-img: "img/used/7547.jpg"
catalog: false
tags:
    - 后缀自动机
    - 后缀数组
    - 树状数组
---
## [题目](https://www.lydsy.com/JudgeOnline/problem.php?id=2780)
### 题目大意
给定$n$个模板串和$m$个查询串，输出每个查询串在多少个模板串中出现过

### Sample Input
```
3 3
abcabcabc
aaa
aafe
abc
a
ca
```
### Sample Output
```
1
3
1
```
### 题解
只想到了$SA$做法……

先对于模板串拼成的大串建后缀数组，对于每个查询串，都可以二分找到$lcp$是ta自己的$rank$区间，然后问题就转换成了求一些区间里不同数（后缀在的串的标号）的个数，这样就是一个经典的离线树状数组/莫队的题了

$O(nlogn)$竟然跟$O(n\sqrt n)$跑的时间一样……

### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<cmath>
# include<algorithm>
using namespace std;
const int MAX=4e5+5;
struct p{
    int l,r,id;
    bool operator< (const p &a)
    const{
        if(r!=a.r) return r<a.r;
        return l<a.l;
    }
}qu[MAX];
int N,q,n,m,Ans,k,cnt;
int a[MAX],tax[MAX],rk[MAX],sa[MAX],h[MAX],het[MAX],ID[MAX],num[MAX],ans[MAX],s[MAX],pre[MAX],L[MAX];
char b[MAX];
bool cmp(int *a,int x,int y,int w) {return a[x]==a[y]&&a[x+w]==a[y+w];}
void rsort()
{
    for(int i=0;i<=m;++i)
      tax[i]=0;
    for(int i=1;i<=n;++i)
      ++tax[rk[h[i]]];
    for(int i=1;i<=m;++i)
      tax[i]+=tax[i-1];
    for(int i=n;i>=1;--i)
      sa[tax[rk[h[i]]]--]=h[i];
}
void Suffix()
{
    for(int i=1;i<=n;++i)
      rk[i]=a[i],h[i]=i;
    m=127+N,rsort();
    for(int p=1,w=1,i;p<n;w+=w,m=p)
      {
      	for(p=0,i=n-w+1;i<=n;++i)
      	  h[++p]=i;
      	for(i=1;i<=n;++i)
      	  if(sa[i]>w) h[++p]=sa[i]-w;
      	rsort(),swap(rk,h),rk[sa[1]]=p=1;
      	for(i=2;i<=n;++i)
      	  rk[sa[i]]=cmp(h,sa[i],sa[i-1],w)?p:++p;
      }
    int j,k=0;
    for(int i=1;i<=n;het[rk[i++]]=k)
      for(k=k?k-1:k,j=sa[rk[i]-1];a[i+k]==a[j+k];++k);
}
int lowbit(int x) {return x&-x;}
void change(int x,int dis)
{
	for(int i=x;i<=n;i+=lowbit(i))
	  s[i]+=dis;
}
int GET_SUM(int x)
{
	int ans=0;
	for(int i=x;i;i-=lowbit(i))
	  ans+=s[i];
	return ans;
}
int ask(int l,int r) {return GET_SUM(r)-GET_SUM(l-1);}
int main()
{
    scanf("%d%d",&N,&q);
    for(int i=1,_n;i<=N;++i)
      {
      	scanf("%s",b+1),_n=strlen(b+1);
      	for(int j=1;j<=_n;++j)
      	  a[++n]=b[j],ID[n]=i;
      	a[++n]='z'+i;
      }
    a[n--]=0,Suffix(),k=sqrt(n);
    for(int i=1;i<=n;++i)
      pre[i]=L[ID[sa[i]]],L[ID[sa[i]]]=i;
    for(int i=1,_n,_L,_R;i<=q;++i)
      {
      	scanf("%s",b+1),_n=strlen(b+1),_L=1,_R=n;
      	for(int j=1,ans,l,r,L;j<=_n;++j)
      	  {
      	  	l=_L,r=_R,ans=_L;
      	  	while(l<=r)
      	  	{
      	  		int mid(l+r>>1);
      	  		if(a[sa[mid]+j-1]>=b[j]) ans=mid,r=mid-1;
      	  		else l=mid+1;
            }
            if(a[sa[ans]+j-1]!=b[j]) {_L=1e9;break;}
            L=ans,l=_L,r=_R,ans=_R;
            while(l<=r)
            {
                int mid(l+r>>1);
                if(a[sa[mid]+j-1]<=b[j]) ans=mid,l=mid+1;
                else r=mid-1;
            }
            _L=L,_R=ans;
          }
        if(_L<=_R) qu[++cnt]=(p){_L,_R,i};
      }
    sort(qu+1,qu+1+cnt);
    for(int i=1,L=1;i<=cnt;++i)
      {
      	while(L<=qu[i].r)
      	{
      		if(pre[L]) change(pre[L],-1);
      		change(L++,1);
		}
		ans[qu[i].id]=ask(qu[i].l,qu[i].r);
	  }
    for(int i=1;i<=q;++i)
      printf("%d\n",ans[i]);
    return 0;
}
```
$SAM$的做法其实也差不多

对所有串建广义$SAM$，然后查询串在$SAM$上对应着一个结点（如果存在子串的话），那么答案就是这个点子树里不同数（模板串编号）的个数（即$right$集合）

子树对应着一段连续的$dfs$序，所以还是转换成了上面的问题

### 代码
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
using namespace std;
const int MAX=5e5+5;
int N,Q;
char a[MAX],b[MAX];
struct SAM{
	struct p{
		int l,r,id;
		bool operator< (const p &a)
		const{
			return r<a.r;
		}
	}qu[MAX];
	struct q{
		int x,y;
	}c[MAX];
	int l,r,L,num,tot,cnt;
	int h[MAX],siz[MAX],len[MAX],ID[MAX],fa[MAX],s[MAX],In[MAX],pre[MAX],re[MAX],__L[MAX],ans[MAX];
	int son[MAX][26];
	SAM() {r=l=1;}
	void add(int x,int y) {c[++num]=(q){h[x],y},h[x]=num;}
	int lowbit(int x) {return x&-x;}
	void change(int x,int dis)
	{
		for(int i=x;i<=tot;i+=lowbit(i))
		  s[i]+=dis;
	}
	int GET_SUM(int x)
	{
		int ans=0;
		for(int i=x;i;i-=lowbit(i))
		  ans+=s[i];
		return ans;
	}
	int ask(int l,int r) {return GET_SUM(r)-GET_SUM(l-1);}
	void ins(int x,int id)
	{
		int tt=r;
		len[r=++l]=++L,ID[r]=id;
		for(;tt&&!son[tt][x];tt=fa[tt])
		  son[tt][x]=r;
		if(!tt) return void(fa[r]=1);
		int qwq=son[tt][x];
		if(len[qwq]==len[tt]+1) return void(fa[r]=qwq);
		len[++l]=len[tt]+1,ID[l]=id;
		for(int i=0;i<26;++i)
		  son[l][i]=son[qwq][i];
		fa[l]=fa[qwq],fa[qwq]=fa[r]=l;
		for(int i=tt;son[i][x]==qwq;i=fa[i])
		  son[i][x]=l;
	}
	void dfs(int x=1)
	{
		siz[x]=1,re[In[x]=++tot]=x;
		for(int i=h[x];i;i=c[i].x)
		  dfs(c[i].y),siz[x]+=siz[c[i].y];
	}
	void GET_POINT(int _n,int id)
	{
		int x=1;
		for(int i=1;i<=_n;++i)
		  {
		  	x=son[x][b[i]-'a'];
		  	if(!x) break;
		  }
		if(x) qu[++cnt]=(p){x,0,id};
	}
	void Init()
	{
		for(int i=2;i<=l;++i)
		  add(fa[i],i);
		dfs();
		for(int i=1;i<=cnt;++i)
		  qu[i].r=In[qu[i].l]+siz[qu[i].l]-1,qu[i].l=In[qu[i].l];
	}
	void Solve()
	{
		Init();
		for(int i=1;i<=tot;++i)
		  pre[i]=__L[ID[re[i]]],__L[ID[re[i]]]=i;
		sort(qu+1,qu+1+cnt);
		for(int i=1,L=1;i<=cnt;++i)
		  {
		  	while(L<=qu[i].r)
		  	{
		  		if(pre[L]) change(pre[L],-1);
		  		change(L++,1);
			}
			ans[qu[i].id]=ask(qu[i].l,qu[i].r);
		  }
		for(int i=1;i<=Q;++i)
		  printf("%d\n",ans[i]);
	}
}_S;
int main()
{
	scanf("%d%d",&N,&Q);
	for(int i=1,_n;i<=N;++i,_S.L=0,_S.r=1)
	  {
	  	scanf("%s",a+1),_n=strlen(a+1);
	  	for(int j=1;j<=_n;++j)
	  	  _S.ins(a[j]-'a',i);
	  }
	for(int i=1,_n;i<=Q;++i)
	  scanf("%s",b+1),_S.GET_POINT(_n=strlen(b+1),i);
	return _S.Solve(),0;
}
```
