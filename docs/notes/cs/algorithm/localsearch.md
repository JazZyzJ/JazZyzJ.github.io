# Intro

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

# Example

## Vertex Cover

!!! question "Vertex Cover"

    给定一个图$G=(V,E)$，我们需要找到一个最小顶点集合$min S\subseteq V$，使得每一条边至少有一个顶点在集合$C$中。


可行解解空间：$C= \{ S|S \text{ is a vertex cover} \}$

cost函数：$f(S)=|S|$

- 进行neighbor定义：

定义neighbor是与当前解$S$相差一个顶点的解，即

$$ 
N(S)= \{ S'|S' \text{ is a vertex cover and } |S' \text{can be obtained from } S \text{ by + or - one vertex} \} 
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
P(S\rightarrow S') = e^{-\frac{f(S')-f(S)}{KT}}
$$

- 分子上的$f(S')-f(S)$称为坡度
    - 坡度大时，概率较小，不倾向于选择爬坡 

- 称$T$为temperature，$K$为Boltzmann常数。
    - 当$T$比较大时，系统十分活跃，容易进行爬坡，反之不易
    - 在系统初期，$T$比较大，容易爬坡，随着$T$逐渐减小，爬坡越来越难，这个操作被称为**模拟退火**（simulated annealing gradully decrease T）




坏处在于：难以分析🥲，myc老师这种做算法分析的人不喜欢（）

但是做应用的人喜欢，因为实验结果导向，还好用（）


## Hopfield Network Problem

!!! question "Hopfield Network"

    



