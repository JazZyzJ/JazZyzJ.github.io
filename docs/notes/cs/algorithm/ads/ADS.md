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

- Zig
Single Rotation

没有G，只转一次X

- Zig-zag
  
Double Rotation
转两次X

- Zig-zig

Single Rotation 
先转X再转P

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

最好的势能函数应该确保n次操作的credit值尽可能小

例子 对Splay树进行均摊分析

若选深度作为势能函数，无法计算所有节点的深度变化，因此不能选择
若选择所选转节点的子节点个数，可以行得通，但是X、G的变化太大，所得到的界太松
最终选择的技巧是：

**对节点数取对数 得到树的秩**

即：$\Phi(D)=Rank=\sum\log S(n)$

对势能函数分析时，如果进行完全分析，需要对每个节点的credit进行计算，但是这样计算太复杂，因此我们进行放缩，只对所寻找的目标节点的势能进行分析，这样可以简化问题

需要用到下面的引理：

**Lemma:**

$$
if:a+b \leq c 
$$

$$
then:\log a + \log b \leq 2\log c + 2
$$

![ads-6](/../../../../assets/pics/ads/ads-6.png)

我手写了一下这三个放缩的过程：

![ads-7](/../../../../assets/pics/ads/ads-7.jpg)















## Red-Black Tree
### Intro
AVL树是通过实现高度平衡来进行优化

而红黑树是通过实现颜色平衡来进行优化

在树的代码中，遇到了叶子结点时需要```if```判定空，常用一个哨兵节点来避免大量的```if```语句，但是为了避免空间的浪费，在红黑树中，我们使用了**一个**NIL节点来代替哨兵节点,所有的叶子节点和根节点都指向NIL节点

红黑树的性质：

- 每个节点要么是红色，要么是黑色
- 根节点是黑色
- 每个叶节点（NIL节点）是黑色
- 如果一个节点是红色，那么它的两个子节点都是黑色
- 对于每个节点，从该节点到其所有后代叶节点的路径上，均包含相同数目的黑色节点

**black height**：从某节点（不包含自身）到叶节点的黑色节点的个数

Lemma: 一个有N个内部节点的红黑树的树高至多为$2\log(N+1)$

证明：设空树的树高为0

归纳假设：

$$
for\ any\ node\ x, sizeof(x) \geq 2^{bh(x)}-1
$$

归纳奠基显然在空树上成立

归纳演绎：

对一个非空树时：$h(x) \leq k$ 成立，现在证明$h(x) \leq k+1$也成立

对一个x满足$h(x) \leq k+1$，其子树的黑高要么等于x的黑高，要么等于x的黑高-1（子树的根节点是红色）

因为：

$$
h(child) \leq k
$$

则有：
$$
sizeof(child) \geq 2^{bh(child)}-1 \geq 2^{bh(x)-1}-1
$$
即子树的节点数有一个下界

由于：
$$
bh(Tree) \geq h(Tree)/2
$$
则：
$$
Sizeof(Tree)=N \geq 2^{bh(Tree)}-1 \geq 2^{h/2}-1
$$
得证





### Operation

- Insertion
两个性质需要保持：

1. Key值保证二叉树
2. 红黑树性质

默认插入的节点为红色，插入后不会破坏第五条性质，但是继续插入后可能会遇到连续的红色节点，这时需要进行补救

需注意性质2可能因为插入的是空树被破坏，这时需要将根节点涂黑


接下来的分类讨论其实就是根据叔叔的颜色进行旋转

**Case 1**

叔叔节点是红色

<div align="center">
<img src="/../../../../assets/pics/ads/ads-8.png" alt="ads-8" height="200px" width="300px">
</div>

插入一个节点后，父亲与叔叔都是红色，这时将父亲涂黑，发现左树的黑高比右树的黑高多1，再将叔叔涂黑，发现整棵树对于树外部分整体黑高+1，这时将祖父涂红，满足了红黑树的性质，但是会遇到祖父的父亲是红色的情况，这时需要递归处理

这也是插入唯一需要迭代的操作

**Case 2**

<div align="center">
<img src="/../../../../assets/pics/ads/ads-9.png" alt="ads-9" height="200px" width="300px">
</div>

叔叔的颜色是黑色，且插入节点靠近叔叔节点，此时无法通过传递祖父的黑色维持黑高，需要进行旋转操作，将插入节点与父亲进行旋转，进入Case 3

**Case 3**

<div align="center">
<img src="/../../../../assets/pics/ads/ads-10.png" alt="ads-10" height="200px" width="300px">
</div>

先将爸爸染黑，爷爷染红，此时左子树的黑高比右子树的黑高多1，因此要进行右旋转平衡黑高




- Delete 

