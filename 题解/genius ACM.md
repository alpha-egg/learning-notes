**题目：** genius ACM

贪心+倍增+归并

[109. 天才ACM - AcWing题库](https://www.acwing.com/problem/content/111/)

**题解：**

~~让我红温三天的题目，必须写个题解记录一下。~~

这题的思路不难。首先题目中给出了校验值的定义：

从集合 $S$ 中取出 $M$ 对数(即 $2\times M$ 个数，不能重复使用集合中的数，如果 $S$ 中的整数不够 $M$ 对，则取到不能取为止)，使得“每对数的差的平方”之和最大，这个最大值就称为集合 $S$ 的“校验值”。

我们要把序列 $A$ 分成若干段，使得每一段的“校验值”都不超过 $T$ ，然后求最少得段数。

可以想到，因为每一段都是连续的，所以我们只要让每一段包含的数尽量多就能使得段数最少。

为了使复杂度符合要求，使用倍增的做法：

初始令 $L=R=1$ ，$p=1$ 。

当 $R\leq N$ 时，我们进行如下操作：

- 如果区间 $\left[L,R+p  \right]$ 的校验值小于 $T$ ，那么我们就将 $R+=p,\;p*=2$ 
- 如果大于 $T$ ，我们就将 $p/=2$ 
- 如果 $p=0$ 我们就找到一段区间 $\left[L,R  \right]$ 满足包含的数最多。我们将总段数加1，然后令 $L+1,R+1$ ，找到下一个区间的右端点

下面的是大概的代码

```c++
int ans=0;
int R=1,L=1;
while(R<=N){
	int p=1;
	while(p){
		if(R+p>=N){p>>=1; continue;}  //防止越界
		if(cal(L,R)<T){R+=p; p*=2;} //cal函数表示计算校验值
		else p>>=1; 
	}
	L=++R;   //注意不要写成 R++ ！！！
	ans++;
}
```

整体的思路就是上面，但是我们还没有解决如何求校验值的问题。

其实也简单，如果我们要求区间 $\left[L,R  \right]$ 的校验值，我们只需要将该区间排序，然后根据校验值的定义计算即可，但是这样做复杂度容易超，因为我们每次计算校验值都需要重新排序，**这时候我们就要用归并排序的方法简化复杂度** 。

我们可以用一个数组 $u$ 来存排序后的数组 $a$ 。如果我们已经将区间 $\left[L,R  \right]$ 排序完成，我们要计算区间 $\left[L,R +p\right]$ 的校验值，我们只需要将 $\left[R+1,R+p  \right]$ 进行排序，然后再用归并排序的方法将 $\left[L,R  \right]$ 和 $\left[R+1,R+p  \right]$ 合并就得到了有序的区间 $\left[L,R +p\right]$ 。

代码如下：

```c++
#include<bits/stdc++.h>
#define REP(i,a,b) for(int i=(a); i<=(b); i++)
#define PER(i,a,b) for(int i=(a); i>=(b); i--)
using namespace std;
typedef long long ll;

inline ll rd(){
    ll x=0,f=1; char c=getchar(); while(!isdigit(c)){if(c=='-') f=-1; c=getchar();}
    while(isdigit(c)){x=10*x+c-'0'; c=getchar();} return x*f;
}

int K,N,M;
ll T;
vector<int> a,u;

//计算校验值
ll cal(vector<int> &v,int l,int r){
    ll res=0;
    int len=min(M,(r-l+1)/2);
    REP(i,0,len-1) res+=(ll)(v[r-i]-v[i+l])*(v[r-i]-v[i+l]);
    return res;
}

//排序+判断校验值是否小于T
bool mer(vector<int> &v,int l,int mid,int r){
    int i=l,j=mid+1;
    REP(k,j,r) u[k]=a[k];  //将新增片段拷贝给u，防止前面失败后的u污染
    sort(u.begin()+mid+1,u.begin()+r+1); 
    vector<int> tmp;
    while(i<=mid&&j<=r){
        if(u[i]<=u[j]) tmp.push_back(u[i++]);
        else tmp.push_back(u[j++]);
    }
    while(i<=mid) tmp.push_back(u[i++]);
    while(j<=r) tmp.push_back(u[j++]);
    if(cal(tmp,0,tmp.size()-1)>T) return 0;
    for(int i1=l,i2=0;i1<=r;i1++,i2++) u[i1]=tmp[i2]; //只有满足条件才拷贝
    return 1;
}

void sol(){
    N=rd(),M=rd(),T=rd();
    int ans=0;
    a.resize(N+1);
    REP(i,1,N) a[i]=rd();
    u.assign(a.begin(),a.end());
    int L=1,R=1;
    while(R<=N){
        int p=1;
        while(p){
            if(R+p>N){p>>=1; continue;}
            if(mer(u,L,R,R+p)) {R+=p; p*=2;}
            else p>>=1;
        }
        ans++;
        L=++R;
    }
    cout<<ans<<endl;
}

int main(){
    K=rd();
    while(K--) sol();
    return 0;
}

```
