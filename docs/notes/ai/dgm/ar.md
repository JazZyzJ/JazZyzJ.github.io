---
comment: true
---

# Autoregressive Model

!!! abstract
    自回归模型是使用非常广泛的一种生成模型，在先前的粗略学习中我大概知道了部分架构如CNN、RNN、Attention等，但是没有细致的了解过，这一部分会参考MIT那份教材多一些


## Conditional Distribution Modeling
>条件分布建模并不只适用于autoregressive model

在先前的学习中我们使用多个独立随机变量分布来建模，但是在实际生活中其实很多分布是相互依赖的

在先前的学习中（VAE），我们是利用独立的潜变量进行建模，但是这需要strict assumption for high-dimensional data（eg., 32x32x3 pixels）但是这很难：

<div style="text-align: center;">
    <img src="/../../../../assets/pics/ai/dgm/ar/ar1.png" style="width: 80%;">
</div>

所以我们引入另一种方式：采用条件分布建模

Chain Rule在这里被应用：

任何一个联合分布都能被写成条件分布的乘积：

$$
p(A, B, C) = p(A)p(B|A)p(C|A, B)
$$

并且是

- 任意排列的：

<div style="text-align: center;">
    <img src="/../../../../assets/pics/ai/dgm/ar/ar2.png" style="width: 80%;">
</div>

- 任意分解的：

<div style="text-align: center;">
    <img src="/../../../../assets/pics/ai/dgm/ar/ar3.png" style="width: 80%;">
</div>


!!! example "Case Study"

    === "Case 1"

        <div style="text-align: center;">
            <img src="/../../../../assets/pics/ai/dgm/ar/ar4.png" style="width: 100%;">
        </div>

        这适用于每个output依赖之前所有的output，如language model或者pixel prediction

    === "Case 2"        

        <div style="text-align: center;">
            <img src="/../../../../assets/pics/ai/dgm/ar/ar5.png" style="width: 100%;">
        </div>

    === "Case 3"

        <div style="text-align: center;">
            <img src="/../../../../assets/pics/ai/dgm/ar/ar6.png" style="width: 100%;">
        </div>

        模型将noisy images($x_T$)转变为clean images($x_0$)的过程是diffusion process


在对联合分布进行建模时：我们使用神经网络对条件分布进行建模

$$
p(A, B, C) = p_{\theta}(A)p_{\phi}(B|A)p_{\psi}(C|A, B)
$$

- 理论上来说每个p拥有自己的weight，如果使用weight sharing则意味着inductive bias


## Autoregression

**Auto**：self

- using its own outputs for next prediction

**Regression**：

- estimating relationship between variables

!!! note 

    - 自回归是指在推理过程中的行为(inference-time)

    - 但是训练时(training-time)不一定是自回归的(eg., teacher-forcing就采用真实目标输出作为输入以加速训练，没有表现自回归特性)
    

In General：

$$
\begin{align}
p(x_1, x_2, \cdots, x_n) &= p(x_1)p(x_2|x_1)p(x_3|x_1, x_2) \cdots p(x_n|x_1, x_2, \cdots, x_{n-1}) \\
&= \prod_{i=1}^{n} p(x_i|x_1, x_2, \cdots, x_{i-1})
\end{align}
$$


- $x$ can be any representation:

    并不要求是序列的或者时间相关的(sequencial/temporal)

    例如任意维数的向量

- $x$ can be any order or partition

    order：$x_n$ 到 $x_1$ 也是一种合法的生成顺序

    partition：每个x都可以是标量、向量、张量

- $p(\dot|\dot)$ can take any form

    可以是各种形式的数据结构，离散连续都是支持的

## Inductive Bias

在使用Chain Rule时，我们的公式是不涉及近似的，是一个完全准确的表达形式，但是这会导致模型参数爆炸💥，所以我们需要考虑到一些归纳偏置

通常来说每个p都是不同的映射，但我们引入shared architecture(RNN, CNN, Transformer)和shared weight $\theta$后就出现了approximation



## Inference of AR

在原始Chain Rule中，我们可以得到这样的推理过程：

<div style="text-align: center;">
    <img src="/../../../../assets/pics/ai/dgm/ar/ar7.png" style="width: 100%;">
</div>






    