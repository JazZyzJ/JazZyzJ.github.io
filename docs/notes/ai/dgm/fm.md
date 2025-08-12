---
comments: true
---


# Flow Matching

!!! abstract 
    
    在这一节中，我们来看一个与Diffusion Models非常相似的模型，Flow Matching。

    我在这一节的内容参考的是[MIT 6.S184](https://diffusion.csail.mit.edu/)。这个课程主要采用微分方程的视角来讲解Flow Matching和Diffusion，这个思想在Stefano的课上只是浅浅的提及，这里会详细展开。

## Intro

### Intro to ODE

我们先来从数学的角度看看ODE-based的Flow Matching。

从Flow的名字出发，想象一条河流，以及一片漂浮在河流上的叶子🍃

- Vector Field: $\mathbf{u}_t(x)$

    这是最本质的概念，我们可以理解为描述河水流动的速度场，这也是我们在生成模型中需要学习的

    !!! definition 

        $$
        \mathbf{u}: \mathbb{R}^d \times [0,1] \to \mathbb{R}^d \quad (\mathbf{x}, t) \mapsto \mathbf{u}_t(\mathbf{x})
        $$

        接受两个输入：空间位置$\mathbf{x}$和时间$t$，输出一个d维速度向量$\mathbf{u}_t(\mathbf{x})$，告诉我们在t时刻，$\mathbf{x}$这个点的河水流速和方向

    注意到我们这里的速度有角标$t$，这是因为速度场是随时间变化的，被称为非稳恒的（non-autonomous）

---

- Trajectory: $\mathbf{X}_t$

    可以理解为一片叶子在河流中随时间变化的实际路径

    !!! definition 

        $$
        \mathbf{X}_t: [0,1] \to \mathbb{R}^d \quad t \mapsto \mathbf{X}_t
        $$

        接受一个时间$t$，输出一个d维位置$\mathbf{X}_t$，告诉我们在t时刻，这片叶子在什么位置，还需要满足约束：在任何时刻$t$，叶子在位置$\mathbf{X}_t$的流速为$\mathbf{u}_t(\mathbf{X}_t)$

---

- ODE: 

    这就是上述约束的数学表达，也就是我们说的ODE（Ordinary Differential Equation）
    
    !!! definition 
        $$
        \frac{d\mathbf{X}_t}{dt} = \mathbf{u}_t(\mathbf{X}_t) \tag{ODE}
        $$

        $$
        \mathbf{X}_0 = \mathbf{x}_0 \tag{Initial Condition}
        $$

---


- Flow: $\psi_t(\mathbf{x}_0)$

    flow就是ODE的解，任何一个初始位置给定为$\mathbf{x}_0$的叶子，在时间$t$时，都会到达$\psi_t(\mathbf{x}_0)$这个位置

    !!! definition 

        $$
        \psi_t: \mathbb{R}^d \to \mathbb{R}^d \quad \mathbf{x}_0 \mapsto \psi_t(\mathbf{x}_0)
        $$

        $$
        \frac{d}{dt}\psi_t(\mathbf{x}_0) = \mathbf{u}_t(\psi_t(\mathbf{x}_0)) \tag{flow ODE}
        $$

        $$
        \psi_0(\mathbf{x}_0) = \mathbf{x}_0 \tag{flow initial condition}
        $$

    对于一个给定初始条件的叶子，他的trajectory of ODE是被：

    $$
    \mathbf{X}_t = \psi_t(\mathbf{x}_0)
    $$

    覆盖的，这个flow**将所有可能的初始点以及他们各自随着时间变化的路径都封装在一个函数中**
    

---

!!! summary

    Vector fields, ODEs, and flows are, intuitively, three different descriptions of the same object: 

    <div align="center">
        **vector fields define ODEs whose solutions are flows**
    </div>



??? example "Linear Autonomous Vector Field"

    <div align="center">
        <img src="/../../../../assets/pics/ai/dgm/fm/fm1.png" style="width: 100%;">
    </div>

    
### Intro to Flow Matching

上面的例子中我们假设有一个这样的向量场，那是十分方便计算的，但现实中ODE很难找到一个解析解，因为计算ODE的解：

$$
\psi_t(\mathbf{x}_0) = \mathbf{x}_0 + \int_0^t \mathbf{u}_s(\psi_s(\mathbf{x}_0)) ds
$$

其中的被积函数$\mathbf{u}^\theta$是神经网络，其中还嵌套着解自身，是递归结构的。

我们只能用数值方法来求解。

!!! note "Euler's Method"

    最原始的方法求解ODE，就是Euler's Method。

    欧拉法的本质源于导数的定义，在时间$t$时，我们处在$\mathbf{x}_t$这个位置，在计算得到当前瞬时速度后，我们假设在接下来h时间内，速度恒定不变，我们沿着这个方向走一小段。**本质是用直线段近似真实轨迹**

    $$
    \mathbf{X}_{t+h} = \mathbf{X}_t + h \mathbf{u}_t(\mathbf{X}_t) \quad \text{ t = 0, h, 2h, ..., 1-h}
    $$

    欧拉法的局限性也很明显，因为速度是瞬时的，所以用直线段近似真实轨迹，每一步都会产生离散化误差，为了减小这个误差，我们需要将h设定为非常小的一个值，但这也导致了计算次数的大幅增加（n = 1/h）。这是一个一阶方法，其单步误差为$O(h^2)$，总误差为$O(h)$。

    
!!! note "Runge-Kutta Method"

    为了减小误差，我们可以使用Runge-Kutta Method，这里我们介绍一个二阶方法：Huen's Method
    
    $$
    \mathbf{X'}_{t+h} = \mathbf{X}_t + h \mathbf{u}_t(\mathbf{X}_t) \tag{initial guess}
    $$

    $$
    \mathbf{X}_{t+h} = \mathbf{X}_t + \frac{h}{2} \left( \mathbf{u}_t(\mathbf{X}_t) + \mathbf{u}_{t+h}(\mathbf{X'}_{t+h}) \right) \tag{final guess}
    $$

    休恩法意识到速度是会变化的，所以采用了h时间段的平均速度来近似真实轨迹。这对应着定积分中的梯形法则，而梯形法则显然比欧拉法的矩形法则来的更精确

在引入了数值求解方法后，我们有就将一个理想中连续的河流用离散化计算来simulation的方法了，现在我们引入flow model

!!! definition "Flow Modeling"

    我们的核心思想是将一个简单的distribution经过ODE的约束得到一个复杂的distribution

    $$
    \mathbf{X_0} \sim p_{init} \quad \text{random init}
    $$

    $$
    \frac{d}{dt}\mathbf{X}_t = \mathbf{u}^\theta_t(\mathbf{X}_t) \quad \text{ODE}
    $$

    其中$\mathbf{u}^\theta_t: \mathbb{R}^d \times [0,1] \to \mathbb{R}^d$是神经网络，我们的目标是：

    $$
    \mathbf{X}_1 \sim p_{data} \equiv \psi_1^\theta(\mathbf{X}_0) \sim p_{data}
    $$

这样我们就得到了sample部分的算法：

<div align="center">
    <img src="/../../../../assets/pics/ai/dgm/fm/fm2.png" style="width: 100%;">
</div>


## Train



    

    






    







    
    





