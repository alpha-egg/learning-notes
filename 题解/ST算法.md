**ST表：**

在解决 RMQ 问题（区间最值问题）时，ST 表是一个非常重要的方法。RMQ 问题要求区间 $\left[ l,r \right]$ 的最大值，会有多次询问。我们知道一个长度为 $n$ 的序列有 $n^{2}$ 个区间，如果老老实实的求出每个区间的最值，那复杂度为 $O(n^{2})$ ，显然不是很优。而横空出世的ST算法可以以 $O(n\log n)$ 的复杂度将序列进行预处理，然后满足 $O(1)$ 查询。

ST算法运用了倍增的思想，其大致思路如下：用数组 $f[i][j]$ 来表示序列 $a$ 区间 $\left[ i,i+2^{j}-1 \right]$ 的最大值。在预处理出法 $f[i][j]$ 数组时，用递推的方法。首先可以明确递推的边界是 $f[i][0]=a[i]$ 。递推的公式为

$$
f[i][j]=\max\{f[i][j-1],f[i+2^{j-1}][j-1]\}
$$

递推的思想是讲要求的区间进行二分，最大值就是两部分最大值的最大值。

王续期太强！！！！

```c++
int a[M],f[M][22];
void setf(){
	for(int i=1;i<=n;i++) f[i][0]=a[i];
	int t=log(n)/log(2);
	for(int i=1;i<=t;i++) for(int j=1;j<=n;j++) 
	f[i][j]=max(f[j][i-1],f[j+(1<<(i-1))][i-1]);
}
```

查询的时候就找到最大的 $k$ 满足 $2^{k}< r-l+1\leq 2^{k+1}$ ，也就是满足长度不超过区间长度的 $k$ ，则区间 $\left[ l,r \right]$ 的最大值为 $max(f[l][k],f[r-2^{k}+1][k])$ ，

```c++
int st(int l,int r){
	int k=log(r-l+1)/log(2);
	return max(f[l][k],f[r-(1<<k)+1][k]);
}
```

整体代码如下

```c++
#include<bits/stdc++.h>

#define REP(i,a,b) for(int i=(a); i<=(b); i++)

#define PER(i,a,b) for(int i=(a); i>=(b); i--)

using namespace std;

const int M=1e5+50;

typedef long long ll;

  

inline ll rd(){

    ll x=0,f=1; char c=getchar(); while(!isdigit(c)){if(c=='-') f=-1; c=getchar();}

    while(isdigit(c)){x=10*x+c-'0'; c=getchar();} return x*f;

}

  

int logn[M],f[M][22];

int n,m,a[M];

  

void setl(){

    logn[1]=0;

    REP(i,2,n) logn[i]=logn[i>>1]+1;

}

  

void setf(){

    REP(i,1,n) f[i][0]=a[i];

    int t=logn[n];

    REP(i,1,t){

        REP(j,1,n-(1<<i)+1) f[j][i]=max(f[j][i-1],f[j+(1<<(i-1))][i-1]);

    }

}

  

int st(int l,int r){

    int k=logn[r-l+1];

    return max(f[l][k],f[r-(1<<k)+1][k]);

}

int main(){

    n=rd(), m=rd();

    REP(i,1,n) a[i]=rd();

    setl(),setf();

    while(m--){

        int l=rd(),r=rd();

        printf("%d\n",st(l,r));

    }

    return 0;

}
```