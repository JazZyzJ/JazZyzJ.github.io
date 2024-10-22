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




- **Delete**

总体的思路是将删除节点增黑，然后通过转移这个黑色到父亲节点，在进行黑高的平衡，当然最简单的情况就是删除节点是红色，这时直接删除即可，其次删除节点删除节点只有一个孩子（只能是红孩子）代替他爹后变黑即可

然后我们讨论删除节点是黑叶子的情况，这里需要引入一个双黑的概念，即删除叶节点是黑色，那么在删除它之后将它的黑色增加到2，再通过旋转和染色操作平衡黑高，最后将其变为单黑NIL

**Case 1**

<div align="center">
<img src="/../../../../assets/pics/ads/ads-11.png" alt="ads-11" height="200px" width="300px">
</div>

删除节点是黑色，父亲也是黑色但是兄弟是红色，此时将兄弟染黑，父亲染红，但是这样会使黑高右移，因此需要进行左旋转，旋转之后进入到Case 2



**Case 2**

<div align="center">
<img src="/../../../../assets/pics/ads/ads-12.png" alt="ads-12" height="200px" width="300px">
</div>

此时兄弟节点的颜色是黑色且侄子是双黑，这时候甩锅给父亲，将父亲染黑，此时右路黑色多了一个，然后在分类讨论，父节点本来是红色就结束了，染黑即可；父节点本来是黑色，就继续递归

此时需要注意的是一种情况就是所删除的节点一直递归到根节点时，一路上都没有红色节点，就会进入到**Case 4**，这时候需要进行旋转操作

我画出了这个操作的过程：

<div align="center">
<img src="/../../../../assets/pics/ads/ads-15.jpg">
</div>

这时候问题出现了，如果侄子有红色，那么在Case 2中我们将兄弟染红的操作就是错误的，因为这样会导致两个连续红色，这就进入了Case 3

**Case 3**

<div align="center">
<img src="/../../../../assets/pics/ads/ads-13.png" alt="ads-13" height="200px" width="300px">
</div>

远侄子是黑色，近侄子是红色，此时将红色侄子与其黑色父亲互换，会导致左侄子这条路的黑高多1，因此需要进行右旋转，然后这时远侄子就是红色了，进入Case 4

**Case 4**

<div align="center">
<img src="/../../../../assets/pics/ads/ads-14.png" alt="ads-14" height="200px" width="300px">
</div>

这个情况是：X是待删除节点，他的身上有两个黑色标记，他的兄弟是黑的，远侄子一定是红的，父亲和近侄子可红可黑，这时候的操作步骤：

1. 将父亲和兄弟的颜色互换，为了避免父亲是红色导致右侧出现连续的红色，将侄子染黑
2. 这时候的黑高右边有可能不变，也可能增大1向右边倾斜，进行一次左旋转
3. 这时候待删除节点就位于叶节点上了并且黑高也平衡了，将其删除即可

如果从逆向的角度思考，具备直接删除一个黑色节点的条件，就是这个节点是叶节点并且他所在的路径上黑高多了一，这就是一直往左边塞黑色的原因，就是要让他成为一个可以直接删除的节点

总结：

1. 先看兄弟是不是黑
2. 再看侄子有没有红
3. 侄子有红看远侄子
4. 转化到远侄子为红
5. 远侄为红一顿操作


Number of Rotations:

|  | AVL | Red-Black |
| --- | --- | --- |
| Insertion | $\leq 2$ | $\leq 2$ |
| Deletion | $O(log n)$ | $\leq 3$ |



## B+ Tree

### Intro

B树是一种平衡的树状数据结构，主要用于在磁盘上存储数据，减少磁盘的I/O次数


B-Tree with order B:

- 结构性质

1. 根节点要么是叶子节点，要么有2到B个孩子
2. 每个非叶子节点有
3. 所有叶子节点在同一层
   
- 数值性质

1. 数值全部存放在叶子结点
2. 非叶子结点中存储routing element，用于确定子树中数值的分布,并且有介于$\lceil B/2 \rceil$到$B$个孩子,称为他的fanout
3. 非叶子节点的元素个数+1=孩子个数
4. 叶子节点存的元素个数在$\lceil B/2 \rceil$到$B$之间根节点可以存储1到B个元素
5. 一个存储了$K$个元素$e_1,e_2,e_3,e_4,e_K$的internal node 有$v_1,v_2,v_3,v_4,v_K$这些叶子结点：

$$
e_i=minimun\ element\ in\ leaves\ under\ v_{i+1}
$$

$$
for\ keys\ in\ leaves\ i\ in\ v_i: Tv_1 \leq ... \leq Tv_K
$$

补充的性质：N表示元素个数

1. leaves $\leq N/ \lceil B/2 \rceil$
2. nodes $\leq 2N/ \lceil B/2 \rceil$
3. elements $\leq 2N$ 这里用nodes数乘B得到的似乎是4N，但需要注意的是路径上的节点存储的数值全部是元素的副本，总数不会超过2N
4. 

$$
{ Height }=\log _{\lceil B/2 \rceil} \frac{N}{\lceil R / 2\rceil} \leqslant \log _{\lceil B / 2\rceil} N=\frac{\log _2 N}{\log _2 B-1} \leqslant O(\log_B N)
$$

这意味着B树的高度是非常低的（相较于二叉树的$O(\log_2 N)$），因此可以减少磁盘的I/O次数

![ads-16](/../../../../assets/pics/ads/ads-16.png)


### Operation

- FindKey

对routing element进行二分查找，找到对应的叶子节点，然后对叶子节点进行二分查找

这样得到的CPU时间复杂度为$O(\log_N)$，也就是说，增加B树的阶数不会改变查找时间

磁盘的I/O次数为$O(\log_B N)$

- InsertKey

对于没有满的节点，直接插入即可，如果满了，则需要进行分裂操作，将节点分裂为两个节点，并将中间的元素更新到父节点，如果父节点也满了，则需要进行递归操作，直到根节点

有一个小的优化措施是在自己满员兄弟未满的时候，将元素移动到兄弟节点，这样可以减少分裂的次数，实现时需要定位到兄弟节点，因此添加一个指针指向兄弟节点，但是这个操作只会停留在共享一个父节点的节点之间，而避免遍历所有节点

CPU时间复杂度为$O(B\log_B N)$，磁盘的I/O次数为$O(\log_B N)$

- DeleteKey

删除操作与插入类似，当所在的叶子节点在删除当前元素后元素个数小于$\lceil B/2 \rceil$，则需要进行合并操作，左右节点有$\geq \lceil B/2 \rceil+1$，时，可以直接借一个元素过来，否则需要将当前节点与左兄弟或右兄弟合并

时间复杂度与插入相同


## Leftist Tree

### Intro

满足堆的传统性质，即父节点的值小于等于子节点的值

同时满足左式堆的性质：

对任意一个节点到一个没有两个孩子的节点的距离称为节点的**null path length**，记作$npl(x)$，显然，对于一个有左孩子和右孩子的节点，$npl(x)=1+\min(npl(lc),npl(rc))$，对于叶子节点，$npl(x)=0$，在这里我们定义$npl(null)=-1$

!!! note "一个没有两个孩子的节点"
    这里注意这个定义，如果一个节点只有一个孩子，那么无论他的这个孩子有多长，他的null path length都是0，因为我们定义了空节点的null path length为-1，而计算时：

    $$
    npl(x)=1+\min(npl(lc),npl(rc))=1+(-1)=0
    $$


在左式堆中，$npl(lc(x)) \geq npl(rc(x))$，即左孩子的null path length总是大于等于右孩子的null path length，因此，左式堆也被称为**左偏树**

对于一个有n节点的二叉树，若满足左式堆的性质，则有$npl(root) \leq \log_2(n+1)$，即对于一个长度为r的right path，至少有$2^r-1$个节点


### Operation

- Merge(H1, H2)

![ads-17](/../../../../assets/pics/ads/ads-17.png)

??? example "Merge Example"
    ![ads-23](/../../../../assets/pics/ads/ads-23.png)

- DeleteMin(pointer to u)

![ads-18](/../../../../assets/pics/ads/ads-18.jpg)

## Skewed Heap

### Intro

很简单，不需要额外空间维护null path length，只需要在每次合并(递归返回)的时候对根节点的两个孩子进行一次旋转即可

可以看作是self-adjusting leftist heap


### Operation

- Merge(H1, H2)

在操作时不进行npl的比较，直接左右互换，其余操作与左式堆相同

不支持删除和decrease key操作

??? example "Skewed Heap Merge Example"
    ![ads-24](/../../../../assets/pics/ads/ads-24.png)


## Inverted File Index

实现搜索引擎的方法：

没怎么听。。。




## Binomial Queue

**Heap-Ordered Tree**: 满足堆性质的完全二叉树，包括Min-Heap Max-Heap

**Priority Queue**:不按照队列先进先出的顺序，而是按照某种优先级进出

二项队列不是单独的一棵树，而是一个森林

森林中的每一个树被称为二项树 binomial tree 并定义树的高度，高度为0的树是由一个节点构成

高度为k的树是由两个高度为k-1的树合并而成，在这里合并时，根节点最小的树作为新的根节点，同时将另一个树作为新的**根节点**的孩子，也就是说另外一棵树是直接连接到根节点上的

![ads-19](/../../../../assets/pics/ads/ads-19.png)

$B_k$表示高度为k的二项树，$B_k$有$2^k$个节点，根节点的度数为k，根的孩子从左到右依次为$B_{k-1},B_{k-2},...B_0$，在d深度层有$\binom{k}{d}$个节点

性质：
BQ 中每个高度的树至多有一棵，也就是说，每一个BQ都可以被唯一的表示为一个二进制分解
   


### Operation

- FindMin

每个二项树的根节点都是最小值，因此只需要遍历每个二项树的根节点即可找到最小值，而一个n个节点的BQ中，最多有$\lceil \log_2 n \rceil + 1$棵树，因此时间复杂度为$O(\log n)$

但是，如果需要频繁的进行FindMin操作，则需要维护一个最小值的指针，这样可以将时间复杂度降低到$O(1)$

- Merge

思路就是二进制加法，从最低位开始，单独存在的二项树保留，同时存在的二项树合并成为层数+1的新的二项树

每次合并的时间复杂度是$O(\log 1)$，总共的时间复杂度为$O(\log n)$

- DeleteMin
  
找到最小的元素所在的二项树$O(\log n)$，将这个树单独拿出来$O(\log n)$，去除根节点$O(\log n)$，得到一个新的BQ，这个新的BQ与之前剩下的树进行合并，时间复杂度为$O(\log n)$

![ads-20](/../../../../assets/pics/ads/ads-20.png)

可以看到二项队列中每一个二项树的实现是通过左孩子右兄弟的表示方法实现的

连续的插入操作：均摊时间复杂度是$O(n)$

??? proof "Proof"

    ![ads-21](/../../../../assets/pics/ads/ads-21.png)

    ![ads-22](/../../../../assets/pics/ads/ads-22.png)

    2-c的推导：

    因为每c次操作，可以被分解为1次的step和c-1次的合并，而合并增加的树的个数是-1，所以得到2-c


