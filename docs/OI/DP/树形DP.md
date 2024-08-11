---
tags:
  - DP
  - 待完成
date: 2023-11-01
dg-publish: true
dg-permalink: 树形DP
---

> 树上DFS + DP，一般用 $f_{i,\cdots}$ 表示以 $i$ 为根的子树的状态。

## 树上dp

> [!note] [P1352 没有上司的舞会](https://www.luogu.com.cn/problem/P1352)
> 
### 树上背包

> [!note] [P2014 [CTSC1997] 选课](https://www.luogu.com.cn/problem/P2014)
> 课程里有些课程必须在某些课程之前学习，现在有 $N$ 门功课，每门课有个学分，每门课有一门或没有直接先修课（树），从这些课程里选择 $M$ 门课程学习，问他能获得的最大学分是多少？

先不考虑依赖，令 $dp[u][j]$ 表示以 $u$ 为根，包括 $u$ 在内选 $j$ 门课能得到的最大学分。那么DFS到叶子时 $j=1$ 一定要选 $u$，$dp[u][1]=score[u]$。

依赖关系在转移时处理：  
设处理 u 为根的树总共选修 j 门课程，则儿子们共修 j-1 门课程  
若处理到儿子 v，枚举 v 儿子选修 k 门课程（0 ~ j-1）  
则 u 已处理的儿子只能选择 j-1-k 门课程  

## 换根dp

第一次dfs钦定一个根找答案（同时保存一些子树的**数据**，比如子树大小、子树权值和、子树dp答案，有时还要维护不严格次大值），第二次dfs按照dfs顺序依次翻转每一个儿子当根。

### 步骤

- 第一次dfs，算出以1为根的ans，维护子树信息（siz、dep等）。
- 第二次dfs，对于每个节点 $u$，依次枚举其儿子 $v$ 当作新的树根：
	1. 复制原来所有数组中只涉及到 u/v 的数据
	2. 用这些数据推算出以 $v$ 为根的新数据，并覆盖数组中的数据
		- 这一步往往是最繁琐的，可能需要很多步骤，有时不是 $O(1)$！
	3. 此时，我们就完成了 **“换根”** ——保证每个儿子 $v$ 被换成根时，所有（要用到的）数据都是以 $v$ 为根所得的数据。继续dfs即可。
	4. 从儿子返回，用1中复制的数据还原数组中的数据。

### 习题

#### 注意不是 $O(n)$，要比值排序

> [!note] 签到题
> 给定一颗点带权无根树，请你选定一个根并对这棵树进行深度优先遍历，得到一个点的经过顺序（即dfs序）：$v_1,v_2\cdots v_n$，记点 $u$ 的点权为 $A_u$，请最小化下面式子的值：$\sum^{n}_{i=1} i\times A_{v_i}$

```cpp
#include <bits/stdc++.h>
using std::cin;
using std::cout;
using LL = long long;
using ull = unsigned long long;
using ld = long double;

const int maxn = 2e5+10;

int a[maxn];
LL siz[maxn],sum[maxn];
std::vector<int> edge[maxn];

bool cmp(int x,int y){
	return siz[x]*sum[y] < siz[y]*sum[x];
}

void pdfs(int now,int fa){
	siz[now]=1;
	sum[now]=a[now];
	for(int i : edge[now]){
		if(i==fa) continue;
		pdfs(i,now);
		siz[now]+=siz[i];
		sum[now]+=sum[i];
	}
	std::sort(edge[now].begin(),edge[now].end(),cmp);
}

// 算出以1为根的ans 在此基础上改 
LL id,ans,ANS=LLONG_MAX;
void dfs1(int now,int fa){
	++id,ans+=id*a[now];
	for(int i : edge[now]){
		if(i==fa) continue;
		dfs1(i,now);
	}
}

// 换根 dp ——保证每个儿子被换到根时，所有（要用到的）数据都是以其为根所得的数据 
void dfs2(int u,int fa){
//	cout << u << ": " << ans << '\n';
	ANS=std::min(ANS,ans);
	LL cntsz = 1,cntsum = a[u]; // 多一个儿子累加一次 
	
	for(int v : edge[u]){
		if(v==fa) {
			cntsz+=siz[v],cntsum+=sum[v];
			continue;
		}
		
		// choose a son to flip to the root
		
		// step1: 复制原数据（只会涉及到 u/v/ans） 
		const LL usiz=siz[u],usum=sum[u],vsiz=siz[v],vsum=sum[v],pans = ans;
		
		// step2: update the siz & sum
		siz[u] = usiz-vsiz;
		sum[u] = usum-vsum;
		siz[v] = usiz;
		sum[v] = usum;
		
		// step3: calc the new answer & get deeper
		
		ans = ans - cntsz*vsum + cntsum*vsiz;
		cntsz+=vsiz,cntsum+=vsum;
		// 原v子树被提起来cntsz格，同时已经遍历过的下降vsiz格 
		
		
		LL ccsiz=0,ccsum=0;
		for(int bro : edge[v]){
			if(bro==u) continue;
			if(cmp(u,bro)) {
				// u比其兄弟小
				ccsiz+=siz[bro],ccsum+=sum[bro];
			}
		}
		ans = ans - ccsiz*sum[u] + siz[u]*ccsum;
		// 统计所有 siz/sum 比新子树u更大的siz.sum之和
		// 并插入到正确的位置更新答案 （u左移，兄弟右移）  
		std::sort(edge[v].begin(),edge[v].end(),cmp);
		
		// well, it looks like a sandwich:: 
		// copy & modify -> dfs(son) -> change back
		// add
		dfs2(v,u);
		// step4: recover
		ans = pans;
		siz[u]=usiz,sum[u]=usum,siz[v]=vsiz,sum[v]=vsum;
		std::sort(edge[v].begin(),edge[v].end(),cmp);
	}
}

int main() {
//	freopen("ex_signin4.in","r",stdin);
	int n;
	cin >> n;
	for(int i=1;i<n;i++){
		int u,v;
		cin >> u >> v;
		edge[u].push_back(v);
		edge[v].push_back(u);
	}
	for(int i=1;i<=n;i++) cin >> a[i];
	
	pdfs(1,1);
	dfs1(1,1);
//	for(int i=1;i<=n;i++){
//		ans=id=0;
//		pdfs(i,i);
//		dfs1(i,i);
//		cout << ans << ' ';
//	}
	dfs2(1,1);
	cout << ANS;
	return 0;
}
```


#### 维护次大值：
#待完成 

> [!note] [P3647 [APIO2014] 连珠线](https://www.luogu.com.cn/problem/P3647)
> 线是红色或蓝色的，珠子被编号为 $1$ 到 $n$。这个游戏从一个珠子开始，每次会用如下方式添加一个新的珠子：
> 
> - `Append(w, v)`：一个新的珠子 $w$ 和一个已经添加的珠子 $v$ 用红线连接起来。
> - `Insert(w, u, v)`：一个新的珠子 $w$ 插入到用红线连起来的两个珠子 $u, v$ 之间。具体过程是删去 $u, v$ 之间红线，分别用蓝线连接 $u, w$ 和 $w, v$。
> 
> 每条线都有一个长度。游戏结束后，你的最终得分为蓝线长度之和。
> 
> 给你连珠线游戏结束后的游戏局面，只告诉了你珠子和链的连接方式以及每条线的长度，没有告诉你每条线分别是什么颜色。
> 
> 你需要写一个程序来找出最大可能得分。即，在所有以给出的最终局面结束的连珠线游戏中找出那个得分最大的，然后输出最大可能得分。

```cpp
#include <bits/stdc++.h>
#include <climits>
using std::cin;
using std::cout;
using LL = long long;
using ull = unsigned long long;
using ld = long double;

const int maxn = 200010;

struct Edge {
	int v,w,pre;
} e[maxn<<1];
int head[maxn],tot;
void add(int u,int v,int w) {
	e[++tot]= {v,w,head[u]},head[u]=tot;
}

int n;
LL f[maxn][2],mx1[maxn],mx2[maxn];

void dfs(int now,int fa) {
	for(int p=head[now]; p; p=e[p].pre) {
		int v=e[p].v,w=e[p].w;
		if(v==fa) continue;
		dfs(v,now);
		// +v's贡献
		f[now][0] += std::max(f[v][0],f[v][1]+w);
		f[now][1] += std::max(f[v][0],f[v][1]+w);
		LL val = f[v][0]+w-std::max(f[v][0],f[v][1]+w);
		
		if(mx1[now] < val) {
			mx2[now] = mx1[now]; // 次大值
			mx1[now] = val;      // 最大值
		} else {
			mx2[now] = std::max(mx2[now],val);//!!!不是严格次大值
		}
	}
	f[now][1] += mx1[now];
}

LL ans=0;
void solve(int now,int fa) {
	ans = std::max(ans,f[now][0]);
	int f0=f[now][0],f1=f[now][1];
	int maxn1 = mx1[now],maxn2 = mx2[now];
	
	for(int p=head[now]; p; p=e[p].pre) {
		int v=e[p].v,w=e[p].w;
		if(v==fa) continue;

		// 回退
		f[now][0]-=std::max(f[v][0],f[v][1]+w);
		f[now][1]-=std::max(f[v][0],f[v][1]+w);
		int val1=f[v][0]+w-std::max(f[v][0],f[v][1]+w);
		if(val1==mx1[now]) {
			f[now][1]+=mx2[now]-mx1[now];
		}
		
		f[v][0]+=std::max(f[now][0],f[now][1]+w);
		f[v][1]+=std::max(f[now][0],f[now][1]+w);
		f[v][1]-=mx1[v]; //?????????????????????????
		LL val2=f[now][0]+w-std::max(f[now][0],f[now][1]+w);
		
		if(mx1[v] < val2) {
			mx2[v] = mx1[v]; // 次大值
			mx1[v] = val2;   // 最大值
		} else {
			mx2[v] = std::max(mx2[v],val2);
		}
		f[v][1]+=mx1[v]; //?????????????????????????
		solve(v,now);
		
		// 恢复
		f[now][0]=f0,f[now][1]=f1;
		mx1[now]=maxn1,mx2[now]=maxn2; 
	}
}

int main() {
	cin >> n;
	for(int i=1; i<n; i++) {
		int x,y,w;
		cin >> x >> y >> w;
		add(x,y,w),add(y,x,w);
	}
	dfs(1,1);
	solve(1,1);
	cout << ans;
	return 0;
}
```


