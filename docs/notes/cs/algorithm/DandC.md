# Divide and Conquer

核心思想：将较大的问题分解为较小的子问题，分别解决子问题，然后将子问题的解合并为原问题的解

Recursively:

**Divide** the problem into a number of subproblems

**Conquer** the subproblems by solving them recursively

**Combine** the solutions to the subproblems into the solution for the original problem

$$
T(n) = aT(n/b) + f(n)

N 是原问题的规定大小, f(n) 是划分和合并子问题的解的时间
$$

## Example

### Maximum Subarray

常见套路是均匀的一分为二

在这里，我们将数组一分为二，分别求出左半部分的最大子数组和右半部分的最大子数组，然后考虑跨越中点的最大子数组

横跨子序列的求算方法：

从中间向两边扫描，分别求出由中间向左的最大子数组和由中间向右的最大子数组，然后合并


## Analysis

一个盘子有N个点，找到距离最近的两个点


## Three Methods for Solving Recurrence

假定$n/b$ 是整数


1. Substitution Method

猜测一个结果，然后用数学归纳法证明

2. Recursion-tree Method

画出一些例子，用树表示递归的展开

3. Master Method
