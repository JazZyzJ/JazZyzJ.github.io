# Intro 

> Dealing with HARD problems

目标：得到一个近似解

## Approximation Ratio

我们通过**近似比**$\rho(n)$来衡量近似算法的好坏，称一个算法是**$\rho(n)$-近似算法**。它的意义是：规模为n的输入下，近似解$C$与最优解$C^*$的比值，也就是说

$$
max(\frac{C}{C^*}, \frac{C^*}{C}) \leq \rho(n)
$$

算法的近似比与问题规模相关，通常情况下$\rho(n)$随着n的增大而增大



## Approximation Scheme

最优解的近似方案(Approximation Scheme)，指的是一类近似算法，对给定$\epsilon > 0$，它是一个(1+$\epsilon$)近似算法

- **多项式时间近似方案**（PTAS）：对于任意给定$\epsilon > 0$，能在多项式时间复杂度完成计算，记作$O(n^{f(\frac{1}{\epsilon}})$
    - **完全多项式时间近似方案**（FPTAS）：对于任意给定$\epsilon > 0$，能在多项式时间复杂度完成计算，记作$O\left(n^{O(1)}\left(\frac{1}{\varepsilon}\right)^{O(1)}\right)$

设计算法时，考虑如下三个方面：

- 最优性（Optimality）：解的质量
- 效率（Efficiency）：计算成本
- 全部实例（All instances）：通用性

对这几个方面进行trade-off

- A+B：对于特殊情况的精准快速算法
- A+C：针对全部实例的精准算法
- B+C：近似算法




# Example

## Bin Packing


Next fit 可以得到一个近似比为2的解

First fit 1.7

Best fit 大于等于1.7



不用太多考虑边界情况：因为最坏情况下special case 不会太多

### On-line Algotithm

在装箱问题中是在线算法的 并且没有回溯

在线算法很难对抗数据分布，因为看不到数据的整体分布

对于所有在线算法，装箱问题都到不到5/3这个近似比

!!! quote


### Off-line Algorithm

## Knapsack Problem




## K-Center Problem





