---
tags:
  - 矩阵乘法
  - DP
  - 树链剖分
date: 2023-07-03
publish: true
permalink: 动态DP
---

## 简述

将普通的矩阵乘法中的乘变为加，加变为 $\max$ 操作。

动态dp = 带修dp = 广义矩阵乘法 + 树剖 + DP

板子就是跑一个没有上司的舞会，带修改的版本。

此时需要树剖，重儿子比较特殊（在另一条链上），转移看作线性dp，构造广义乘法矩阵，顺着根到修点的路径，一路乘上去得到答案。

最后取max。。。 

有分配率的俩运算如$\{\times,+\},\{+,\max\}$

## 附录

### 广义矩阵乘法：

$C_{i,j} = (A_{i, 1}\otimes B_{1,j}) \oplus (A_{i,2}\otimes B_{2,j})\oplus \cdots \oplus (A_{i, n}\otimes B_{n,j})$

考察这种广义矩阵乘法是否满足结合律：


$$
\begin{aligned}
((AB)C){i,j} &= \bigoplus^{p}(AB){i,k}\otimes C \\
&= \bigoplus_{k=1}^{p}\left( \left(\bigoplus_{t=1}^{q} A_{i,t}\otimes B_{t,k}\right) \otimes C_{k,j}\right) \\
(A(BC)){i,j} &= \bigoplus^{q}A_{i,t}\otimes (BC){t,j} \\
&= \bigoplus^{q} \left(A_{i,t}\otimes \left(\bigoplus_{k=1}^{p} B_{t,k} \otimes C_{k,j}\right)\right) \\
\end{aligned}
$$


**若 $\bigotimes$ 运算满足交换律、结合律，且 $\bigotimes$ 对 $\bigoplus$ 存在分配律**，广义矩阵乘法满足结合律：


$$
((AB)C)_{i,j} = (A(BC))_{i,j} = \bigoplus_{k=1}^{p}\bigoplus_{t=1}^{q} \left(A_{i,t}\otimes B_{t,k}\otimes C_{k,j}\right)
$$


### 参考

https://www.cnblogs.com/luckyblock/p/14430820.html


