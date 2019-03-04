---
layout:     post
title:      "[CF1042X]Codeforces Round #510 (Div. 2)"
date:       2018-09-23
author:     "Dispwnl"
header-img: "img/used/megumin.jpg"
catalog: true
tags:
    - 比赛
---
### [A　　Benches](http://codeforces.com/contest/1042/problem/A)

#### 题目大意
有$n$个椅子，每个椅子上有$a_i$个人，你需要往这$n$个椅子上面塞$m$人，问塞完后人数最多的椅子最少和最多有多少人

#### 题解
简单贪心即可

注意比较最少人数和最大的$a_i$

#### 代码
```
# include<bits/stdc++.h>
using namespace std;
const int MAX=1e5+5;
int a[MAX],n,m,maxn,sum;
int main()
{
	cin>>n>>m;
	for(int i=1; i<=n; i++)
	  cin>>a[i],maxn=max(maxn,a[i]);
	for(int i=1; i<=n; i++)
	  sum+=a[i];
	sum+=m;
	sum=sum/n+(sum%n!=0);
	printf("%d %d\n",max(sum,maxn),maxn+m);
}
```

### [B　　Vitamins](http://codeforces.com/contest/1042/problem/B)

#### 题目大意
有$n$个药片，药片里含维他命$ABC$中的一种或多种，问集全维他命$ABC$最小花费是多少

#### 题解
发现就那么几种组合情况，暴力枚举即可

#### 代码
```
# include<bits/stdc++.h>
using namespace std;
int n,ans=1e8;
map<string,int> ma;
void work(string &a)
{
	int L=a.length();
	for(int i=0;i<L-1;++i)
	  for(int j=i+1;j<L;++j)
	    if(a[i]>a[j]) swap(a[i],a[j]);
}
int main()
{
	scanf("%d",&n);
	string a;
	ma["A"]=ma["B"]=ma["C"]=ma["AB"]=ma["BC"]=ma["AC"]=ma["ABC"]=1e8;
	for(int i=1,c;i<=n;++i)
	  cin>>c>>a,work(a),ma[a]=min(ma[a],c);
	ans=min(ans,ma["A"]+ma["B"]+ma["C"]);
	ans=min(ans,ma["ABC"]);
	ans=min(ans,ma["ABC"]);
	ans=min(ans,ma["ABC"]);
	ans=min(ans,ma["A"]+ma["BC"]);
	ans=min(ans,ma["B"]+ma["AC"]);
	ans=min(ans,ma["C"]+ma["AB"]);
	ans=min(ans,ma["AB"]+ma["AC"]);
	ans=min(ans,ma["AB"]+ma["BC"]);
	ans=min(ans,ma["AC"]+ma["BC"]);
	if(ans==1e8) return printf("-1"),0;
	return printf("%d",ans),0;
}
```
### [C　　Array Product](http://codeforces.com/contest/1042/problem/C)

#### 题目大意
有$n$个数，有两种操作：

- 选择$a_i,a_j(i\not =j)$，$a_j=a_j\times a_i$且$a_i$从此不能再被选中
- 选择$a_i$，$a_i$从此不能再被选中，这个操作只能用一次

用$n-1$次操作后只会剩下一个数，问要这个数最大的操作顺序是什么

#### 题解
统计大于$0$，等于$0$，小于$0$的$a_i$数量，根据贪心的思想：

- 如果全是负数和$0$或者全是正数，就用操作一合并
- 如果全是正数和$0$，$0$合并到一起，最后把合并出来的唯一一个$0$用操作二去掉，剩下的正数就随便合并即可
- 如果全是负数和正数，负数为偶数个就直接合并，为奇数个把最大的负数操作二搞掉再合并就可以了

基本上就是这几种情况，贪心即可

#### 代码
```
# include<bits/stdc++.h>
# define LL long long
using namespace std;
const int MAX=2e5+5;
struct p{
	int x,id;
	bool operator< (const p &a)
	const{
		return x<a.x;
	}
}a[MAX],b[MAX],c[MAX];
int n,num1,num2,num3;
LL ans;
int A[MAX];
void work1()
{
	for(int i=1;i<n;++i)
	  printf("1 %d %d\n",i,i+1);
	exit(0);
}
void work2()
{
	for(int i=1;i<num1;++i)
	  printf("1 %d %d\n",a[i].id,a[i+1].id);
	printf("2 %d",c[1].id);
	exit(0);
}
void work3()
{
	sort(b+1,b+1+num2);
	for(int i=1;i<num2-num2%2;++i)
	  printf("1 %d %d\n",b[i].id,b[i+1].id);
	if(num2%2) printf("1 %d %d\n",b[num2].id,c[1].id);
	for(int i=1;i<num3;++i)
	  printf("1 %d %d\n",c[i].id,c[i+1].id);
	if(num2>1||num1) printf("2 %d\n",c[num3].id);
	if(num1&&num2>1) printf("1 %d %d\n",b[num2-num2%2].id,a[1].id);
	for(int i=1;i<num1;++i)
	  printf("1 %d %d\n",a[i].id,a[i+1].id);
	exit(0);
}
void work4()
{
	sort(b+1,b+1+num2);
	for(int i=1;i<num2-1;++i)
	  printf("1 %d %d\n",b[i].id,b[i+1].id);
	printf("2 %d",b[num2].id);
	exit(0);
}
void work5()
{
	for(int i=1;i<=num2;++i)
	  a[++num1]=b[i];
	for(int i=1;i<num1;++i)
	  printf("1 %d %d\n",a[i].id,a[i+1].id);
	if(num3) printf("2 %d\n",c[1].id);
	exit(0);
}
void work6()
{
	sort(b+1,b+1+num2);
	for(int i=1;i<num2-1;++i)
	  printf("1 %d %d\n",b[i].id,b[i+1].id);
	printf("2 %d\n",b[num2].id);
	if(num1&&num2>1) printf("1 %d %d\n",b[num2-1].id,a[1].id);
	for(int i=2;i<=num1;++i)
	  printf("1 %d %d\n",a[i-1].id,a[i].id);
	exit(0);
}
void work7()
{
	sort(b+1,b+1+num2);
	for(int i=1;i<num2-1;++i)
	  printf("1 %d %d\n",b[i].id,b[i+1].id);
	printf("1 %d %d\n",b[num2].id,c[1].id);
	if(num1||num2>1) printf("2 %d\n",c[1].id);
	for(int i=1;i<num1;++i)
	  printf("1 %d %d\n",a[i].id,a[i+1].id);
	if(num1&&num2>1) printf("1 %d %d",a[num1].id,b[num2-1].id);
	exit(0);
}
int main()
{
	scanf("%d",&n);
	for(int i=1;i<=n;++i)
	  {
	  	scanf("%d",&A[i]);
	  	if(A[i]>0) a[++num1]=(p){A[i],i};
	  	if(A[i]<0) b[++num2]=(p){A[i],i};
	  	if(A[i]==0) c[++num3]=(p){A[i],i};
	  }
	if(num1==n||(num2%2==0&&!num3)) work1();
	if(num3==1&&!num2) work2();
	if(num3>1) work3();
	if(num2==n) work4();
	if(num2%2==0&&num3<=1) work5();
	if(num2%2&&num3==1) work7();
	if(num2%2&&!num3) work6();
	return 0;
}
```

### [D　　Petya and Array](http://codeforces.com/contest/1042/problem/D)
#### 题目大意
给定一个序列，要求找出字段和小于$t$的子段个数

#### 题解
用树状数组维护点$i$前面有多少个前缀和大于$sum_i-t$，从左到右扫一遍即可，复杂度$O(n\log n)$

#### 代码
```
# include<bits/stdc++.h>
# define tl (k<<1)
# define tr (k<<1|1)
# define mid (l+r>>1)
# define LL long long 
using namespace std;
const int MAX=2e5+5;
struct p{
	int id;
	LL x;
	bool operator< (const p &a)
	const{
		return x<a.x;
	}
}a[MAX];
int n,id;
LL t,ans;
int s[MAX],ID[MAX];
LL sum[MAX],TA[MAX];
LL read()
{
	LL x=0,fl=1;
	char ch=getchar();
	for(;!isdigit(ch);fl=(ch=='-')?-1:1,ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x*fl;
}
int lowbit(int x)
{
	return x&-x;
}
void change(int x,int dis)
{
	for(int i=x;i<=id;i+=lowbit(i))
	  s[i]+=dis; 
}
int Sum(int x)
{
	int ans=0;
	for(int i=x;i;i-=lowbit(i))
	  ans+=s[i];
	return ans;
}
int ask(int x,int y)
{
	if(x>y) return 0;
	return Sum(y)-Sum(x-1);
}
int look(LL A)
{
	int l=1,r=id,ans=id+1;
	while(l<=r)
	{
		if(TA[mid]>=A) ans=mid,r=mid-1;
		else l=mid+1;
	}
	return ans;
}
int main()
{
	n=read(),t=read();
	for(int i=1;i<=n;++i)
	  a[i].x=read(),a[i].id=i;
	for(int i=1;i<=n;++i)
	  a[i].x+=a[i-1].x,sum[i]=a[i].x;
	a[n+1].id=n+1,a[0].x=-1e18;
	sort(a+1,a+2+n);
	int IDD=0;
	for(int i=1;i<=n+1;++i)
	  {
	  	if(a[i].x!=a[i-1].x) TA[++id]=a[i].x;
	  	if(!a[i].x) IDD=id;
	  	ID[a[i].id]=id;
	  }
	change(IDD,1);
	for(int i=1;i<=n;++i)
	  {
	  	int L=look(sum[i]-t+1);
	  	ans+=ask(L,id),change(ID[i],1);
	  }
	return cout<<ans,0;
}
```

### [E　　Vasya and Magic Matrix](http://codeforces.com/contest/1042/problem/E)
#### 题目大意
一个$n\times m$的矩阵，每次可以走到一个值小于当前点$(x,y)$的点$(x',y')$，代价为$(x-x')^2+(y-y')^2$，给定一个起点，问期望代价为多少

#### 题解
首先按值排个序，$i$点的期望$f_i$就为
$f_i=\frac{\sum_{j=1,a_j<a_i}^{i}f_j+(x_i-x_j)^2+(y_i,y_j)^2}{i-1}$

发现可以拆开：$f_i=\frac{\sum_{j=1,a_j<a_i}^{i}f_j+x_i^2+x_j^2-2x_ix_j+y_i^2+y_j^2-2y_iy_j}{i-1}$

几个前缀和就能搞定

#### 代码
```
# include<bits/stdc++.h>
# define LL long long
# define int LL
using namespace std;
const int MAX=2e6+1,mod=998244353ll;
struct p{
	int x,i,j;
	bool operator< (const p &a)
	const{
		return x<a.x;
	}
}a[MAX];
int n,m,r,c,num;
LL f[MAX],U[MAX],sum1[MAX],sum2[MAX],sum3[MAX],sum4[MAX],sum5[MAX];
LL Pow(LL a,LL b)
{
	LL ans=1;
	for(;b;b>>=1,a=a*a%mod)
	  if(b&1) ans=ans*a%mod;
	return ans;
}
main()
{
	scanf("%d%d",&n,&m);
	int IID=0;
	for(int i=1,x;i<=n;++i)
	  for(int j=1;j<=m;++j)
	    scanf("%d",&x),a[++num]=(p){x,i,j};
	scanf("%d%d",&r,&c);
	sort(a+1,a+1+num);
	for(int i=1;i<=num;++i)
	  {
	  	if(a[i].x!=a[i-1].x) IID=i-1,f[i]=(sum3[i-1]-2ll*sum1[i-1]*a[i].i-2ll*sum2[i-1]*a[i].j+sum4[i-1]+sum5[i-1]+(i-1)*a[i].i*a[i].i+(i-1)*a[i].j*a[i].j+mod)%mod,f[i]=f[i]*Pow(i-1,mod-2)%mod;
	  	else f[i]=(sum3[IID]-2ll*sum1[IID]*a[i].i-2ll*sum2[IID]*a[i].j+sum4[IID]+sum5[IID]+IID*a[i].i*a[i].i+IID*a[i].j*a[i].j+mod)%mod,f[i]=f[i]*Pow(IID,mod-2)%mod;
		sum1[i]=sum1[i-1]+a[i].i,sum2[i]=sum2[i-1]+a[i].j,sum3[i]=(sum3[i-1]+f[i])%mod,sum4[i]=(sum4[i-1]+a[i].i*a[i].i)%mod,sum5[i]=(sum5[i-1]+a[i].j*a[i].j)%mod;
	  	if(a[i].i==r&&a[i].j==c) return cout<<(f[i]+mod)%mod,0;
	  }
}
```
### [F　　Leaf Sets](http://codeforces.com/contest/1042/problem/F)
#### 题目大意
给定一棵树，把树的叶子分成多个集合，要求每个集合的叶子距离不超过$k$，问最少可以划分成几个集合

#### 题解
感觉思路挺神仙的……

贪心的思想，按深度处理每个点，记录每个点的子树里的叶子节点到ta的距离，排个序后把相邻的相加不大于$k$的划成一个集合，即从$1$开始的一段区间，其他的各自搞个集合，然后返回大集合里的最长边，ta上面的节点处理这棵子树时就以这条边为代替了

为什么这样划分保证最优呢？假设有三条边$a,b,c(a<b<c)$，且$a+b\leq k,a+c\leq k,b+c>k$，如果$a$和$c$放一个集合里，那这个集合就不能放$b$了，同时这个集合能加进去的长度为$k-c$，如果放$b$，这个集合能加进去的长度为$k-b$，明显是优于放$c$的

那么为什么是返回大集合里的最长边呢？如果返回其他单元素的集合，ta们肯定大于大集合里的最长边，其他子树与ta匹配的限制会变大，所以可能会划分成更多的集合，不是更优的处理

#### 代码
```
# include<bits/stdc++.h>
using namespace std;
const int MAX=1e6+1;
struct p{
	int x,y;
}c[MAX<<1];
int n,k,num,ans;
int du[MAX],h[MAX];
void add(int x,int y)
{
	c[++num]=(p){h[x],y},h[x]=num;
	c[++num]=(p){h[y],x},h[y]=num;
}
int dfs(int x,int f=0)
{
	if(du[x]==1) return 0;
	vector<int> ve;
	for(int i=h[x];i;i=c[i].x)
	  if(c[i].y!=f) ve.push_back(dfs(c[i].y,x)+1);
	sort(ve.begin(),ve.end());
	int L=ve.size()-1;
	for(;L>=1;--L)
	  {
	  	if(ve[L]+ve[L-1]<=k) break;
	  	++ans;
	  }
	return ve[L];
}
int main()
{
	scanf("%d%d",&n,&k);
	for(int i=1,x,y;i<n;++i)
	  scanf("%d%d",&x,&y),add(x,y),++du[x],++du[y];
	int rt=1;
	for(int i=1;i<=n;++i)
	  if(du[i]>1) {rt=i;break;}
	dfs(rt);
	return printf("%d",ans+1),0;
}
```
