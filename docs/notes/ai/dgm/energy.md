---
comment: true
---


# Energy-based Models


## Intro

EBMs 具有以下特点：

- Flexible model arch
- Stable training
- Flexible composition

这点与之前的两类模型不同：

进行一个大体上的区分的话，先前学习的

- VAE、Flow、AR都属于基于似然的模型，他们的特点是具有严格的概率解释和训练目标，但是模型结构相对固定，难以灵活调整。

- GANs属于implicit models，他不具有概率解释，这样使得模型的架构更加灵活，但同时训练不太方便

EBMs也是基于似然的模型

### Recap

回忆我们之前学习的概率模型，我们构建的pdf有以下两点要求：

- Non-negative
- Sum to 1

通常来说构建一个非负的pdf是容易的，用平方、指数、绝对值都可以的，但是归一化并不简单，需要计算积分

???+ note "Common usage"

    - $g_{\mu, \sigma}(x) = e^{-\frac{(x-\mu)^2}{2\sigma^2}} \rightarrow \sqrt{2\pi\sigma^2}$

    - $g_{\lambda}(x) = e^{-\lambda x} \rightarrow \frac{1}{\lambda}$
    
    - Exponential family: Normal, Poisson, beta, gamma, etc
        - $g_{\theta}(x) = h(x) \exp(\theta \cdot T(x)) \rightarrow \exp{A(\theta)}$ where $A(\theta) = \log \int h(x) \exp(\theta \cdot T(x)) dx$

在进行归一化操作后，我们组合这些函数进行建模，就有了：

<div align="center" >
    <img src="/../../../../assets/pics/ai/dgm/energy/e1.png" style="width: 90%;">
    </div>

可以看到这些模型都是将normalization constant确定为1，而在EBMs中，我们不仅不会控制这个常数，甚至不会精确算出来它

## Build

$$
p_{\theta}(x) = \frac{1}{Z(\theta)} e^{f_\theta(x)}
$$

$Z(\theta) = \int e^{f_\theta(x)} dx$ is called the **Partition Function**

可以看到我们使用了指数形式，有以下原因：

- 可以捕获巨大的概率变化，并且一般是使用log-probability
- 很多的概率分布都是指数族的
- 满足一些物理定律
    - 实际上energy这个名字的来源就是物理学（最大熵、热二定律），在这里就是指$-f_\theta(x)$



**Pros:** extreme flexibility(any function $f_\theta$ can be used)

**Cons:** 

- Z如果难以计算，那么sampling就很难进行
- No feature learning
- Curse of dimensionality



## Application

虽然进行单个样本的采样很难，但是对于两个样本点的比较会比较容易，因为做比值后，Z就约去了：

$$
\frac{p_\theta(x)}{p_\theta(x')} = exp(f_\theta(x) - f_\theta(x'))
$$

<div align="center" >
    <img src="/../../../../assets/pics/ai/dgm/energy/e2.png" style="width: 90%;">
    </div>


## Training

在前面的内容中我们讨论了EBMs的定义，以及利用EBMs进行两个样本点比较，但这是建立在已经有一个训练好的EBM基础上，现在我们的问题是如何训练一个EBM，也就是maximize $p_\theta(x)$

一个直观的感受就是，对于这样的分数，如果我们能增大分子，减小分母，那么分数值就会增大，这就是 **Contrastive Divergence**

$$
\max_\theta \nabla_\theta (f_\theta(x_{train}) - f_\theta(x_{sample}))
$$

??? quote "Prove"

    <div align="center" >
    <img src="/../../../../assets/pics/ai/dgm/energy/e3.png" style="width: 90%;">
    </div>

    最后的那步近似就是sample的MC近似

现在的问题来到我们应该如何sample：

因为我们没有办法计算$Z$，所以还是采取比较的思想：

!!! tip "Markov Chain Monte Carlo (MCMC) 方法"

    $$
    \begin{aligned}
    &\textbf{1. Initialize } x^0 \text{ randomly, } t = 0 \\
    &\textbf{2. Let } x' = x^t + \text{noise} \\
    &\quad \begin{cases}
    \text{If } f_\theta(x') > f_\theta(x^t), & \text{let } x^{t+1} = x' \\
    \text{Else} & \text{let } x^{t+1} = x' \text{ with probability } \exp(f_\theta(x') - f_\theta(x^t))
    \end{cases} \\
    &\textbf{3. Go to step 2}
    \end{aligned}
    $$

这有点像模拟退火，为了避免停留在一个local，我们参考了一一个概率选择那个较差的sample