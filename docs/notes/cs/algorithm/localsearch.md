## Intro

局部搜索也是一种近似问题的延伸，我们可以给局部搜索进行一个全局性的定义：

!!! note "定义"

    局部搜索算法是一种基于邻域的搜索算法，它通过在当前解的邻域内进行搜索，来找到更优的解。

	定义一个最优化问题有高维的可行解空间$C= \{ S|S \text{ is feasible} \}$，并且对可行解来说有cost函数$f:C\rightarrow R$，

	我们的目标就是找到一个解$S\in C$使得$f(S)$最小。


基本的解决思路：

- 给定一个初始解$S_0\in C$
- 找到$S_0$的邻居$S'$
- 如果$f(S')<f(S_0)$，那么更新$S_0=S'$
- 重复2，3步骤直到满足某个终止条件

关键的步骤就是定义neighborhood，然后进行better neighbor的搜索。

## Example

### Vertex Cover

!!! question "Vertex Cover"

    给定一个图$G=(V,E)$，我们需要找到一个最小顶点集合$min S\subseteq V$，使得每一条边至少有一个顶点在集合$C$中。


可行解解空间：$C= \{ S|S \text{ is a vertex cover} \}$

cost函数：$f(S)=|S|$

- 进行neighbor定义：

定义neighbor是与当前解$S$相差一个顶点的解，即

$$ 
N(S)= \{ S'|S' \text{ is a vertex cover and } S' \text{can be obtained from } S \text{ by + or - one vertex} \} 
$$

!!! failure "失败的man"

	一种情况的算法设计是将全集设置为初始解，然后每次剪掉一个顶点，但是这样很容易陷入局部最优：

	可以想像一个蜘蛛网，中心点连接所有边缘节点，一旦第一次剪掉中心点，外部的每一个点都不能去掉，但是最优解其实是只保留中心点

- 因此需要爬出local optimum

!!!success "Metropolis Algorithm"
	<span style="font-size: 1.1em">
		核心思想是在选定一个解$S$后，在邻居中随机再找一个解$S'$，如果新的解更优，那么就更新，即使这个$S'$比$S$差，也有一定的概率接受。这样就有机会选到局部差解，从而爬出local optimum。
	</span>


这个概率的设置是：

$$
\text{with given constant } K \text{ and } T
$$

$$
P(S\rightarrow S') = e^{-\frac{|f(S')-f(S)|}{KT}}
$$

- 分子上的$f(S')-f(S)$称为坡度
    - 坡度大时，概率较小，不倾向于选择爬坡 

- 称$T$为temperature，$K$为Boltzmann常数。
    - 当$T$比较大时，系统十分活跃，容易进行爬坡，反之不易
    - 在系统初期，$T$比较大，容易爬坡，随着$T$逐渐减小，爬坡越来越难，这个操作被称为**模拟退火**（simulated annealing gradully decrease T）




坏处在于：难以分析🥲，myc老师这种做算法分析的人不喜欢（）

但是做应用的人喜欢，因为实验结果导向，还好用（）


### Hopfield Network Problem

!!! definition "Hopfield Network"
    输入一个图$G=(V,E)$，每个边的权重$w$

	- 定义configuration $S$，其中$S_v \in \{0,1\}$，
	- 定义一个边$e$为好的，当且仅当：
    $$
	w_{e}S_uS_v \leq 0
	$$

	也就是说正权重边连接S不同的顶点，负权重边连接S相同的点

在设计所有点的configuration时，我们有如下两个目标：

- 最大化好边权重绝对值之和
- 对每个顶点$u$，最大化所有与$u$相连的好边的权重之和

在给出两个定义：

!!! note "Definition"

    - 一个顶点$u$是满意的satisfied，当且仅当：
    $$
    \sum_{\text{邻接好边}}{|w_u|} \geq \sum_{\{邻接坏边}}{|w_u|}
	$$

	- 一个configuration是stable的，当且仅当所有顶点都是满意的


很容易可以看出，一个顶点经过反转，可以使好的边变坏，坏的边变好，也就是说可以通过对顶点的flip操作，来改变configuration

进而引出方法

!!! success "State-Flipping"

    - 随机选取一个configuration $S$
    - while $S$ is unstable with $u$ is not satisfied 
        - flip $u$
    - return $S$

可以证明这个算法一定会在有限步内完成：

!!! quote "势能函数证明"

    定义势能函数$\Phi(S)$为所有好边的权重绝对值的和

	在改变一个顶点$u$时，config变为$S'$，此时势能函数的变化为：

	$$
	\Phi(S')-\Phi(S) = - \sum_{u \text{的好邻边}}{|w_e|} + \sum_{u \text{的坏邻边}}{|w_e|}
	$$

	因为$u$是unsatisfied的，所以等式右边是正的（因为定义边权为整数，也一定大于1），因此势能函数一定递增至少1，最终势能不会超过总的边权重和，因此算法一定会完成（最坏情况就是每个顶点都反转，即$O(W)$复杂度）

	!!! warning "Pseudo-Poly 伪多项式"
	    这个操作的最坏情况是在$W$次操作后停止，我们得到了$O(W)$的时间复杂度，这看上去就是一个多项式时间复杂度的操作，但实际上我们的$W$并不是输入问题的规模，而是输入图的边权重和，对于输入问题的大小，需要对其取对数换算成2进制长度，因此这个算法是伪多项式时间复杂度的

值得注意的是，这个势能函数也就是我们的目标一要达到最大值的函数，因此这个算法也就是 

$$ \text{local search to maximize objective } 1 $$

### Maximum Cut

> NP-Hard

!!! note "定义"
    给定有正整数权重的无向图$G=(V,E)$，a cut (A, B)就是$V$的一个划分，将$V$划分为两个不相交的子集$A$和$B$，

	cut的权重定义为：

	$$
    w(A,B) = \sum_{(u,v)\in E|u\in A, v\in B}w_{uv}
	$$

	也就是所有跨越$A$和$B$的边的权重之和

我们的目标是找到一个cut，使得$w(A,B)$最大

其实是一个Hopfield Network Problem的特例，之前的config是正负两种配置，现在就对应到了A和B，好边也就是现在的cut边（交叉边）

- 使用state-flipping算法解决

    - 这时这个算法是local optimal的，并且是一个近似算法

    - 近似比为2

给出证明：

!!! quote "证明"

    在完成state-flipping后，我们可以得到一组$(A,B)$是stable的

	for any $u\in A$ and his neighbors $v$构成的边$e=(u,v)$我们有：

	$$
	\sum_{v \in A}{w_e} \leq \sum_{v \in B}{w_e}
	$$

	对所有$u\in A$，我们有：

	$$
    \sum_{u \in A}{\sum_{v \in A}{w_e}} \leq \sum_{u \in A}{\sum_{v \in B}{w_e}}
	$$

	不等号右侧就是$w(A,B)$

	不等号左侧是将所有在$A$内部边的权重相加，由对称性可知边被求和了两次，因此不等式左侧就是$2\sum_{e\text{是内部边}}{w_e}$

	对$B$同理

	将整个图的边权重和记为$W$，则有：

	$$
    W = \sum_{e\text{是A内部边}}{w_e} + \sum_{e\text{是B内部边}}{w_e} + w(A,B)
	$$

	通过上面的不等式，将两个内部边放缩掉，我们得到：

	$$
    w(A,B) \geq \frac{W}{2} \geq \frac{OPT}{2}
	$$


但是刚刚说到了，这个方法的时间复杂度太高，我们进行优化：

在先前选择flip的点时，只要是好边和小于坏边和我们就进行反转，但是这样反转的频率太高，时间上花费太大，我们进行进一步的近似模糊，只有当改进足够大，我们才反转

- flip only when after flipping：


$$
w(A',B') \geq (1+\frac{\epsilon}{|V|})w(A,B)
$$

由于一个不等式：

$$
(1+\frac{\epsilon}{n})^{\frac{n}{\epsilon}} \geq 2
$$

这个不等式就是说我要让$w(A',B')$翻倍，需要进行$\frac{n}{\epsilon}$次数的反转

而我们知道最多反转次数是$\log W$，因此这个算法是$O(\frac{n}{\epsilon}\log W)$的，达到了多项式时间复杂度

- 这个近似算法的近似比为
$$
2+\frac{\epsilon}{2}
$$

证明的思路和上面类似，在进行第一步反转判断的时候需要更改不等式，不等号右边加入那个$\frac{\epsilon}{|V|}w(A,B)$其他步骤相同




!!! Tip "减少步数"
	其实这就是相当于在local search的时候，我们每步走的太小了因此慢，这个判断条件就是在减少步数


### Kerninghan-Lin algorithm

我们刚才的减少步数方法是一种提高效率的方法，也可以增大步长。

在最基本的情况中我们通过改变一个点的config来进行操作，如果我们同时改变k个点呢？这样就增大了邻居

- 好处是邻居变多加快收敛
- 坏处是邻居要计算，走每一步的时间变长，总共要$\rightarrow$ $O(n^k)$

这里提出了一个算法：

在第一次操作中，选择使得反转收益最大的一个点进行反转得到$(A_1,B_1)$，然后在剩下没有被选中的点中选择使得剩下的点收益最大的点反转得到$(A_2,B_2)$，这样操作下去得到一个邻居集合

$$
N(A,B) = \{(A_1,B_1),(A_2,B_2),...,(A_{n-1},B_{n-1})\}
$$

然后在这个邻居集合中进行local search	

- 得到这个邻居集合时间复杂度是$O(n^2)$的，跑出来效果不错myc老师说（）
