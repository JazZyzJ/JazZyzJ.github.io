# ADS

## AVL Tree
### Intro
在数据集中快速查找、插入、删除、排序等操作时具有较好的效果
 **Target**：Speed up

基本思想：

**height balance**：每个节点的左右子树的高度差（平衡因子BF）不超过1

$$ BF=| h_l - h_r | \leq 1$$

PS:树的高度计算：由根节点到叶节点的最长路径
-> 进行高度平衡

### Operation 
Rotation
在不改变树的相对顺序的情况下，通过旋转操作来调整树的结构，使得树的高度平衡

![ads-1](/../../../../assets/pics/ads/ads-1.png)

想象将需要被提高的节点提高，将需要被降低的节点降低，然后将这两个节点的子树连接到新的节点上

Time Complexity: $O(1)$

因为只有四个节点更新

B、A、y、A的老子

1. RR Rotation or LL Rotation
右子树的右节点插入导致右子树的父节点BF=2，将BF=2的右子节点提高，B连接的左子树连接到A的右子树上

![ads-2](/../../../../assets/pics/ads/ads-2.png)

若遇到多个节点BF非法的情况，只需要对最底部的两个节点进行旋转操作即可
2. LR Rotation or RL Rotation
左子树的右子节点增加导致左子树的父节点BF=2，将左子树的右节点提高两次

![ads-3](/../../../../assets/pics/ads/ads-3.png)

按照片上的解释根据树的合法性可以推出中间步骤
也可以这样理解：
在对Mar进行旋转时，Mar的左位是被占用的，而Mar的父亲是比Mar小的，只有把Jan移开才能给Aug腾出位置
   

### Ananlysis

$n_h$：高度为h的最小节点数
为了保持树有h的高度，至少一个子树的高度为h-1，另一个子树的高度与他相差为1，= n-2
因此：

$$
n_h = n_{h-1} + n_{h-2} + 1
$$


可以得到：<br>

$$ n_h=F_{h+3}-1  
,h \geq -1$$

</p>


## Splay Tree
### Intro
**Target**由空树进行M个连续操作最多需要$O(M \log N)$的时间

即：**Amortized Time Complexity 均摊时间复杂度 = $O(log N)$**

### Operation
将最近访问的节点提升到根节点，这一操作通过旋转来实现

- Zig-zag
Double Rotation
- Zig-zig
Single Rotation

![ads-4](/../../../../assets/pics/ads/ads-4.png)


## Amortized Analysis
均摊法分析是对一系列操作的平均性能进行分析，而不是对单个操作的性能进行分析
bound介于worst和average之间
且均摊界与概率无关
通常有三种方法：Aggregate Accounting Potential

- Aggregate Method
对一系列的操作进行最坏时间情况分析得到$T(n)$，此时：
$$
T(n) \leq \sum_{i=1}^{n} t_i=O(n)
$$
这时$T(n)$逼近于$O(n)$，即均摊时间复杂度为$O(1)$
但是这个方法的难点在于需要确定这个$T(n)$，也就是总的时间开销

在讲解其余的两个方法之前，yy哥说了一个很有趣的故事：
yy在学校门口开了一个小卖部买汽水，每次卖出一瓶成本为1的汽水，学生至少要交一块钱，但是也可以多交钱存到下次使用，汽水的成本就是```actual cost```，而学生交的钱就是```amortized cost```，多出来的钱我们称作```credit```

- Accounting Method
在这个方法中，我们自己设置均摊价值，但需要保证均摊价值不会小于实际价值，即：

$$
\sum \text{amortized cost} \geq \sum \text{actual cost}
$$
(也就是不会赊账)

这时候就会得到一个实际价值的上界

这个方法的过程就是：

- 首先分析实际价值
- 然后设置均摊价值
- 分析credit的值，确保其大于等于0

eg:
![ads-5](/../../../../assets/pics/ads/ads-5.png)

- Potential Method
直观的、一次性定义所有操作的均摊代价，对于某些大量操作需要定义均摊价值的情况，这种方法更加适用

$$
\hat{c}_i-c_i=\text { Credit }_i=\Phi\left(D_i\right)-\Phi\left(D_{i-1}\right)
$$

解释：

$Credit_i$：第i次操作的credit，这时我们引入一个状态得分函数$\Phi(D)$，这个函数的值是一个状态的代价，$D_i$是第i次操作后的状态

但是对于每一次操作，$Credit_i$的数值不仅由当前的状态决定，还受到之前状态的影响，因此我们用$\Phi(D_i)-\Phi(D_{i-1})$来表示

此时，我们可以得到：

$$
\sum_{i=1}^{n} \hat c_i=\sum_{i=1}^{n} c_i+\Phi(D_n)-\Phi(D_0)
$$

恰好在这个求和的过程中，只留下了$\Phi(D_0)$和$\Phi(D_n)$，这两个值是常数，从而我们得到了$\sum_{i=1}^{n} \hat c_i$的值

实际操作中，我们：

- 选择一个合适的状态得分（势能）函数$\Phi(D)$
    - 势能函数的定义要求：$\Phi(D_0)=0$
    - 反应函数的潜在复杂度
- 检查credit的值合法性
- 计算$\sum_{i=1}^{n} \hat c_i$







