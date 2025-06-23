# Randomized Algorithms

## Intro

- Deterministic Algorithms: 确定输入输出

- Randomized Algorithms: 给定固定输入 随机输出

## Example

### Hiring Problem

!!! question "Hiring condition"
    有n个候选人，在结束每一次面试的时候必须立即决定是否录用并且不能反悔

    1. 选择最好的candidate
    2. 在能够保证选出最好的candidate的情况下，尽可能介绍雇佣的次数

很简单直接的思路就是每次面试到了一个在已经面试结束的candidate中最好的，然后录用，也就是一个deterministic算法。

- 但是显然可以发现，如果candidate评估是递增的，那么这个算法会录用所有candidate，显然不是最优的。

!!! definition "Randomly Permute"
    对candidate进行随机排列后再采用确定算法，这样在期望下可以保证hire不超过$\ln{n}$次

    ??? proof "Prove"
        设定一个事件$A_i$，表示第$i$个candidate是前$i$个candi中最好的，那么显然$P(A_i) = \frac{1}{i}$

        对每个候选者$X_i$，如果满足事件$A_i$，那么就录用，$X_i=1$，否则$X_i=0$

        对$X_i$求期望，有
        $$
        E[X_i] = P(A_i) \cdot 1 + (1-P(A_i)) \cdot 0 = \frac{1}{i}
        $$

        对所有$i$求和，有
        $$
        E[\sum_{i=1}^{n} X_i] = \sum_{i=1}^{n} E[X_i] = \sum_{i=1}^{n} \frac{1}{i} \leq \ln{n}+1
        $$

!!! question "Variant"
    现在问题的变体是，如果成本有限，我们只能雇佣一个candi，这时怎样做才能最大化选到最好candi的概率

解决：

1. Randomly Permute
2. 面试前$k$个candidate，但是不雇佣
3. for i = k+1 to n:
   
    如果第$i$个candidate比前$k$个candidate中最好的还要好，那么就雇佣

我们要计算选到最好candi的概率：

$$
P(\text{hire the best}) = \sum_{i=k+1}^{n} P(\text{best is at i } \cap \text{hire him})
$$

我们记等号右侧前面的事件为$A_i$，后面的事件为$B_i$，那么有

$$
= \sum_{i=k+1}^{n} P(A_i)P(B_i|A_i)
$$

A事件的概率比较好计算，对于A发生时B的概率，就是指第$i$个candi出现时，他是在第$k$到第$i-1$个candi中最好的，也即是说在前$i-1$个candi中，最好的那个在$1$到$k$之间，那么有

$$
= \sum_{i=k+1}^{n} \frac{1}{i} \cdot \frac{k}{i-1}
$$

$$
= \frac{k}{n} \sum_{i=k}^{n-1} \frac{1}{i} \geq \frac{k}{n} \ln{\frac{n}{k}}
$$

求导可以得到当$k = \frac{n}{e}$时，概率最大，为$\frac{1}{e}$



### 3SAT Problem

对于一个给定的布尔公式是否存在一种赋值，使得该公式为真，例如：

$$
(x_1 \lor x_2 \lor x_4) \land (\neg x_1 \lor \neg x_2 \lor x_4) \land (x_1 \lor \neg x_2 \lor \neg x_3)
$$

其中$n$是变量个数，$k$是子句clause个数

!!! definition "Monte Carlo"
    对于一个变量$x_i$，我们令其为真的概率或者为假的概率都为0.5，那么每一个clause为真的概率为$\frac{7}{8}$

    可以计算出在期望下，随机赋值使得3SAT问题为真的概率为$\frac{7}{8}k$

    定义：$Y$为满足条件的子句个数，则$E[Y] = \frac{7}{8}k$

    我们知道$Y$的期望值是这个数，那么一定有$P(Y \geq \frac{7}{8}k) \geq 0$（可以反证得到）
    
    这个算法在时间复杂度上可以保证在$O(n)$内解决问题，但是对于解的质量，我们只能保证在期望下有$\frac{7}{8}k$个是正确的

    这种算法称为Monte Carlo算法

!!! definition "Las Vegas"
    我们现在想知道一个概率，就是每跑一次算法，满足条件的子句个数大于等于$\frac{7}{8}k$的概率是多少

    大体的思路是:
    
    取整数$k'$，是式子$\frac{7}{8}k$的取整，这样我们就能将期望拆分：

    $$
    \begin{aligned}
    \frac{7}{8}k = E[Y] &= \sum_{i=0}^{k}{i} \cdot P(Y=i) \\
    &= \sum_{i=0}^{k'} {i} \cdot P(Y=i) + \sum_{i=k'+1}^{k}{i} \cdot P(Y=i) \\
    &\leq k' \cdot \sum_{i=0}^{k'} P(Y=i) + k \cdot \sum_{i=k'+1}^{k} P(Y=i) \\
    &= k' \cdot P(Y \leq \frac{7}{8} k) + k \cdot P(Y \geq \frac{7}{8}k) \\
    &\leq k' + k \cdot P(Y \geq \frac{7}{8}k)
    \end{aligned}
    $$

    所以
    
    $$
    k \cdot P(Y \geq \frac{7}{8}k) \geq {\frac{7}{8}k - k'}
    $$

    取$k' = \frac{7}{8}k$，则有

    $$
    P(Y \geq \frac{7}{8}k) \geq \frac{1}{8k}
    $$

    所以也就是说在期望下，我们需要跑$8k$次算法，才能保证满足条件子句个数大于等于$\frac{7}{8}k$，也就是说解的质量是可以保证的，但是running time只能在期望下求算

    这种算法称为Las Vegas算法


### QuickSort

A Pivot is good if: 能均使得分出的两个子集大小大于原数组的$\frac{1}{4}$

- Idea 1：randomly pick pivot，如果是好的就用，不是就重选

    有$P(\text{good}) = \frac{1}{2}$，期望下每一层递归需要$O(n)$时间，所以总时间复杂度为$O(n \log{n})$

- Idea 2：randomly pick pivot，use it anyway

    同样有时间复杂度是$O(n \log{n})$，


## Implementation 

程序中的random permutation方法：

- 设置随机键值

在$a_1, a_2, \cdots, a_n$中，设置$a_i$的键值为$k_i$，$k_i$为$1$到$n^3$的随机数

键值相同的概率不会超过$\frac{1}{n}

- Random Shuffle

``` python
for i in range(n):
    j = random.randint(1, i)
    swap(a[i], a[j])
```

