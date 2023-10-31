---
tags:
  - 2-SAT
  - tarjan
  - SCC
  - 图论
date: 2023-06-27
dg-publish: true
dg-permalink: 2-sat
---


***二倍点 二倍边 谁没开 谁傻逼***

> SAT 是适定性（Satisfiability）问题的简称。一般形式为 k - 适定性问题，简称 k-SAT。而当 k>2 时该问题为 NP 完全的。**只有2-SAT有多项式复杂度。**

2-SAT，就是 $n$ 个集合，每个集合有2个元素，每个集合只能选取其中的一个元素。

又已知若干个 $\langle a,b \rangle$，表示 $a$ 与 $b$ 矛盾（其中 $a$ 与 $b$ 属于不同的集合）。

判断能否一共选 $n$ 个两两不矛盾的元素满足所有条件。显然可能有多种选择方案，一般题中只需要求出一种即可。

---
等价于：

有 $n$ 个布尔变量 $x_1\sim x_n$，另有若干个需要满足的条件，每个条件的形式都是

**「$x_i$ 为 `true` / `false` 或 $x_j$ 为 `true` / `false`」**

比如 「$x_1$ 为真或 $x_3$ 为假」、「$x_7$ 为假或 $x_2$ 为假」

2-SAT 问题的目标是给每个变量赋值使得所有条件得到满足。

https://www.luogu.com.cn/problem/P4782

---
为什么等价？

这里的$n$个变量就是上面的集合，均为$\{0,1\}$。不妨将第$i$个集合写成$\{F_i=false,T_i=true\}$，分别表示$i=false/true$，则条件「$x_1$ 为真或 $x_3$ 为假」可以看作 $F_1$ 和 $T_3$ 有矛盾，不能同时取。

我们似乎隐隐感觉到要把每个变量（集合）拆成两个元素来解决问题。用图论尝试建模这个条件，将有向边看成「已知某个变量的取值，一定能够推出另一个变量的值」的推导关系。

若设$x_1=F_1=false$，要满足条件，则$x_3$只能取$F_3=false$，连边$F_1->F_3$；  
同样设$x_3=T_3=true$，要满足条件，则$x_1$只能取$T_1=true$，连边$T_3->T_1$。

而设$x_1=T_1=true$，满足条件时，$x_3$两个值都能取，是要连两条边吗？

不！！！！！！

边的定义是「已知某个变量的取值，**一定能够推出另一个变量的值**」的推导关系。虽然是推导关系，但这是不确定的关系，不能连边。

这样就转化成了一个图论问题。对于一个变量$x_i$，若$T_i$经过一条链的推导到$F_i$，则说明$x_i=F_i=false$。图上拓扑序大的点便是变量经过推导后最终的取值。

但是这可能会有环，并且当一个变量的真和假两个点在在一个环里时，是真假互推，同时取真假的叠加态，显然是错误的。

因此我们跑一遍 Tarjan SCC 判断是否有一个变量的两个点在同一个 SCC 中，若有则输出不可能，否则输出方案。

方案？我们不用再跑一边toposort了，缩点的scc_id是反着的拓扑序（dfs越先搜到说明越深），详见代码。

```cpp
#include <bits/stdc++.h>
using std::cin;
using std::cout;
using LL = long long;

const int maxn = 4e6+10; //二倍点 四倍边 

// 2-sat

struct Edge {
	int to,pre;
} e[maxn*2];
int head[maxn],tot;
void addedge(int u,int v) {
	e[++tot] = {v,head[u]}, head[u]=tot;
}

int dfn[maxn],low[maxn],st[maxn],top,id;
bool in_st[maxn];
int n,m;
int scc_id,scc[maxn]; //记录染色 

void tarjan(int now) {
	dfn[now]=low[now]=++id;
	st[++top]=now;
	in_st[now]=1;
	for(int p=head[now]; p; p=e[p].pre) {
		int i = e[p].to;
		if(!dfn[i]) {
			tarjan(i);
			low[now] = std::min(low[now],low[i]);
		} else if(in_st[i]) {
			low[now] = std::min(low[now],dfn[i]);
		}
	}
	if(dfn[now]==low[now]){
		scc_id++;
		while(st[top]!=now){
			scc[st[top]]=scc_id;
			in_st[st[top]]=0;
			top--;
		}
		scc[st[top]]=scc_id;
		in_st[st[top]]=0;
		top--;
		// 弹出now 
	}
}

int main() {
	cin >> n >> m;
	for(int i=1; i<=m; i++) {
		int a,b,va,vb; 
		cin >> a >> va >> b >> vb;
		// 反向思考 一个条件不成立则另一个条件必须成立
		// 真：1~n 假：n+1~2n
		if(va && vb) {
			// a, b 都真，-a -> b, -b -> a
			addedge(a+n,b),addedge(b+n,a);
		} else if(va && !vb) {
			// a 真 b 假，-a -> -b, b -> a
			addedge(a+n,b+n),addedge(b,a);
		} else if(!va && vb) {
			// a 假 b 真，a -> b, -b -> -a
			addedge(a,b),addedge(b+n,a+n);
		} else {
			// a, b 都假，a -> -b, b -> -a
			addedge(a,b+n),addedge(b,a+n);
		}
//		addedge(a+va*n,b+(vb^1)*n);
//      addedge(b+vb*n,a+(va^1)*n);
	}
	for(int i=1; i<=2*n; i++) {
		if(!dfn[i]) tarjan(i);
	}
	// 判断无解 自己和反点没有在一个scc（不取相同值） 
	for(int i=1;i<=n;i++){
		if(scc[i]==scc[i+n]){
			cout << "IMPOSSIBLE";
			return 0;
		}
	}
	cout << "POSSIBLE\n";
	for(int i=1;i<=n;i++){
		if(scc[i+n] < scc[i]) cout << "0 "; // 假的点的编号更小，取假
		else cout << "1 ";
	}
	return 0;
}
```

---
建边也可以用位运算，大概就是：

两个元素相互矛盾，选一个则必定选另一个的补；
两个元素用或连接，不选一个（选了这个的补）则必须选另一个。

两个相互矛盾的条件，一个成立则另一个必定不成立；
两个用或连接的条件，一个不成立则另一个必须成立。

必定能推断的原因是每个集合都有且仅有两个元素可选，一个不可选，则只能选另一个了。

```cpp
if(A=='h') x=0; else x=1;
if(B=='h') y=0; else y=1;
// 多个条件：Aa 或 Bb
// `h汉`在1~n; `m满`在n+1~2n
// 若不选Aa而选择了Ba 则Ba->Bb
add(a+(x^1)*n,b+y*n);
// 若不选Bb而选择了Ab 则Ab->Aa
add(b+(y^1)*n,a+x*n);
```

https://www.luogu.com.cn/problem/P4171

---
还有一个例子，差不多。

邀请人来吃喜酒，夫妻二人**必须去一个**（不是必须去就不是2-SAT了），然而某些人之间有矛盾（比如 A 先生与 B 女士有矛盾，C 女士不想和 D 先生在一起），那么我们要确定能否避免来人之间有矛盾？方案呢？

---
参考文献：

https://www.luogu.com.cn/blog/xcg--123/ti-xie-p4782-mu-ban-2-sat-wen-ti

https://loj.ac/p/3629

https://www.cnblogs.com/FrozenFoggy/p/15824031.html