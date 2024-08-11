---
tags:
  - 哈希
  - 字符串
  - 技巧
date: 2023-07-08
dg-publish: true
dg-permalink: StringHash
---

> 当然也不失为一种骗分技巧，特别是与字典序相关的问题，转化成 lcp。

## Intro

> [!note] [P8023 [ONTAK2015] Tasowanie - 洛谷 | 计算机科学教育新生态](https://www.luogu.com.cn/problem/P8023)
> 给定两个数字串 $A$ 和 $B$，通过将 $A$ 和 $B$ 进行归并，合并得到一个新的数字串 $T$，请找到字典序最小的 $T$。

需要 $O(n\log n)$ 的算法，倍增后缀数组。然而我们可以用二分 + hash 的做法。

题目想让我们做归并排序中的并，字典序最小。但序列不一定是有序的，不能直接并。比如这组数据：

```
4
1 6 6 1
4
1 6 6 5
```

直接并就有可能会出现 `1 1 6 6 5 6 6 1` ，但还有更小的 `1 1 6 6 1 6 6 5`。看代码：

```cpp
while(i<=n&&j<=m){
    if(a[i]<b[j]) t[cnt++]=a[i++];
    else t[cnt++]=b[j++];
}
```

我们并没有判断 $a_i​=b_j$​ 的情况，因为在正常归并排序中，相等不会影响合并的答案。但在这题不同，不能随便放，会对选后面更小的产生影响。

哈希二分可以修正这个问题，通过进制哈希实现 $O(1)$ 比较两个子串是否相等，然后二分找到相等的上界，即 $3,4$ 之间。比较第 $4$ 位发现选上面更好。故此时归并时若 $a_i​=b_j$​ 则优先选 $a_i$ ，只有 $b_j$ 更优才选。

可以预处理出来幂次和前缀哈希sum，不用快速幂。。。公式（左高右低）：

$$
s[l,r]​ = sum_{r​} − B_{r−l+1}*sum_{l−1}
$$

进制 $B$ 随缘选一个（最好比值域稍大），**建议直接ull自然溢出取模**，比直接取模快多了，但是容易被卡。

```cpp
const ull B=5;
ull mi[maxn];
ull hasha[maxn],hashb[maxn];
void prehash() {
	int len = std::max(n,m);
	mi[0]=1;
	for(int i=1; i<=len; i++) {
		hasha[i]=hasha[i-1]*B + getnum(s0[i]);
		hashb[i]=hashb[i-1]*B + getnum(s[i]);
		mi[i]=mi[i-1]*B;
	}
}
inline ull hash(int l,int r,ull hs[]) {
	return hs[r]-hs[l-1]*mi[r-l+1];
}
```

合并类型的二分：
```cpp
void merge() {
	int i=1,j=1,k=0;
	while(k<n){
		int l=0,r=n-j-i+2;
		// r=n; // 爆了 
		if(a[i]==b[j]) {
			while(l+1<r){
				int mid=l+r>>1;
				if(hash(i,i+mid,hasha) == hash(j,j+mid,hashb)) l=mid;
				else r=mid;
			}
		} // 二分长度 
		else r=0;
		if(a[i+r]<b[j+r]) t[++k]=a[i++];
		else t[++k]=b[j++];
	}
}
```

---
## 附录

### 参考/习题

> [!note] [P3763 [TJOI2017] DNA - 洛谷 | 计算机科学教育新生态](https://www.luogu.com.cn/problem/P3763)
> 字符串匹配，允许有小于等于三处不同，问文本串里最多可能有多少模式串。

线性枚举文本串起始点，每次check最多用三次二分查找。