# Discrete Diffusion Models

!!! abstract

    传统的Continuous Diffusion Models可以处理连续的数值数值，但是在实际问题中，其实有很多数据的特征并不连续，比如文字本身、DNA、蛋白质序列、原子类型等，如何处理这类的数据也是需要考虑的问题。

    <div align="center">
        <img src="/../../../../assets/pics/ai/dgm/ddm/ddm1.png" style="width: 100%;">
    </div>

## Intro 

我们很容易去想，为什么不能直接使用先前提到的那些模型，将数据分布离散化然后直接采样呢？有以下几方面的问题需要考虑：

- 采样

<div align="center">
    <img src="/../../../../assets/pics/ai/dgm/ddm/ddm2.png" style="width: 100%;">
</div>

可以看到在连续空间内的采样，或者说在图像问题上，我们是离散的数据，但是可以近似取结果的情况下，这个问题还比较好解决，但是对于非图像的数据，我们一旦采样到远离特定数据点的位置，就变得不好处理了。同时，像文本信息这种问题，近似取值在高维空间并不具备可解释性。

- 训练

离散信息是不能被直接反向传播的，也就意味着我们无法直接更新模型参数

---

现在的在处理文本类信息的方法就是AR方法，我们用链式法则进行概率计算然后逐个inference，但是AR有着我们都知道的不足之处：

- **Sampling "drifts"** - Yann LeCun

    模型的错误是会被不断积累的

- **No resaonable bias for non-language tasks**

    在语言中，从左向右是合理的，但是其他的呢？


- **Slow sampling due to iterative nature**



Ok, let's take a look at what we can do with discrete data🤓



## Modeling

### Score matching on discrete spaces

在这里我们定义了一个 **Concrete Score** 与之前的score做区分，先定义一个新的梯度

$$
\nabla f(x) \equiv [f(y) - f(x)]_{y \text{ neighbor of } x}
$$

然后重写我们的score：

$$
\nabla_x \log p = \frac{\nabla p(x)}{p(x)} = [\frac{p(y)}{p(x)}]_y -1 
$$

!!! bug "Strict define on neighbor"

    但是这里是有点问题的：可以看到因为我们的定义是对y的所有邻居x都进行计算，所以复杂度可以轻松到达$O(N^{2d})$，我们采取一个更local的方式，对一个序列的概率$p(x_1, x_2, \cdots, x_n)$，我们只比较相同位置的值，也就是说：

    y是x的邻居，当且仅当y与x只有一个分量的不同 

    $$
    \frac{p(x_1, \cdots, \hat{x_i}, \cdots, x_d)}{p(x_1, \cdots, x_i, \cdots, x_d)}
    $$

    which is $O(Nd)$


通过一个seq-to-seq网络处理过后我们就有了一个可以感知上下文的BERT-like but non-autoregressive model

<div align="center">
    <img src="/../../../../assets/pics/ai/dgm/ddm/ddm3.png" style="width: 70%;">
</div>



现在我们的目标就是如何学习一个score：$s_\theta(x)$ s.t. $s_\theta(x)_y \approx \frac{p(y)}{p(x)}$

!!! definition "Score Entropy"

    首先写出一个原始的期望表达式，是一个类似于Entropy的计算方式：

    $$\begin{aligned}\min_\theta\mathbb{E}_{x\thicksim p}\sum_{y\neq x}s_\theta(x)_y-\frac{p(y)}{p(x)}\log s_\theta(x)_y\end{aligned}$$ 

    这里需要注意两个点，对于目标函数的设置满足be principled：

    - no negative values
    - recover true values

    第一点是在设计时将分数放在log中，保证一定为正；第二点是上面式子的导数为0的点，恰好就是预测值等于真实值的点



然后就是按照之前的思路，我们想办法对这个Intractable的项进行近似，这里问题集中在两个概率的比值上，我们看看有什么方法可以做近似

这里采用的方式是**Denoising Score Entropy**，也就是模仿denoising score matching的方式

!!! note "Denoising Score Matching"

    Assume $p(x) = \sum_{x_0} p(x|x_0)p_0(x_0)$, which means: $p(x)$ is a convolution of some base distribution $p_0$ and some kernel $p$

    then we rewrite:

    <div align="center">
        <img src="/../../../../assets/pics/ai/dgm/ddm/ddm4.png" style="width: 100%;">
    </div>

    代回原式：

    <div align="center">
        <img src="/../../../../assets/pics/ai/dgm/ddm/ddm5.png" style="width: 100%;">
    </div>


### Sampling using the concrete score

> 现在我们来看如果具体做采样

#### Continuous Time Markov Chain


我们的操作对象$p_t \in \mathbb{R}^{|\mathcal{X}|}$，是一个很大很大的向量。forward过程用一个ODE定义：

!!! definition "Forward Process"
    $$
    dp_t = Q_t p_t
    $$

    转移矩阵$Q_t$需要满足如下定义：

    - Columns sum to 0
    - Non-diagonal elements are $\geq 0$

    因为$Q_t$是随时间变化的，我们把时间参数单独拿出来：
    
    $$Q_t = \sigma(t) Q$$

    这里的$\sigma(t)$是一个随时间变化的函数，用于控制噪声的强度

    !!! example "Choice of $Q$"

        我们有两种方式来对数据进行perturb：

        - Random

            $$Q^\mathrm{uniform}=\begin{bmatrix}1-N&1&\cdots&1\\1&1-N&\cdots&1\\\vdots&\vdots&\ddots&\vdots\\1&1&\cdots&1-N\end{bmatrix}$$

        - Mask

            $$Q^{\mathrm{absorb}}=\begin{bmatrix}-1&0&\cdots&0&0\\0&-1&\cdots&0&0\\\vdots&\vdots&\ddots&\vdots&\vdots\\0&0&\cdots&-1&0\\1&1&\cdots&1&0\end{bmatrix}$$


$Q_t$ 控制着我们的状态转移：

$$p(x_{t+\Delta t}=j|x_t=i)=\delta_{i,j}+Q_t(j,i)\Delta t+O(\Delta t^2)$$

其中$\delta_{i,j}$是Kronecker delta function，用于判断我们当前的位置，然后用$Q_t(j,i)\Delta t$表示我们从一个位置转移到另一个位置的概率

这时我们的概率转移就是：


$$\begin{aligned}p(x_t=j|x_0=i)=\exp(\Sigma(t)Q)(j,i)\end{aligned}$$

其中$\Sigma(t) = \int_0^t \sigma(s) ds$表示噪声的累积量。这个式子就是先前ODE的精确解，表示从i到j的概率


同时对于我们的处理方式上，如果直接对两个seq进行转移开销非常大，所以还是采用之前提到的local方式，对token进行累加：

$$p_{t|0}^\mathrm{seq}(\widehat{\mathbf{x}}|\mathbf{x})=\prod_{i=1}^dp_{t|0}^\mathrm{tok}(\widehat{x}^i|x^i)$$




----------


现在来看一下reverse process

!!! definition "Reverse Process"

    $$dp_{T-t}=\overline{Q}_{T-t}p_{T-t}$$

    其中：

    $$\bar{Q_t}(y, x) = \begin{cases}s_\theta(x,t)_yQ_t(x,y)&x\neq y\\{-}\sum_{z\neq x}\overline{Q}_t^\theta(z,y)&x=y&\end{cases}$$

> 事实上CS236讲到这就结束了，Aaron在课堂上并没有完全展开，我在后面直接看了原论文[Discrete Diffusion Modeling by Estimating the Ratios of the Data Distribution](https://arxiv.org/abs/2310.16834)


### Likelihood training 

直接给出：

$$-\log p_0^\theta(x_0)\leq\mathcal{L}_{\mathrm{DWDSE}}(x_0)+D_{KL}(p_{T|0}(\cdot|x_0)\parallel p_{\mathrm{base}})$$

其中$mathcal{L}_{\mathrm{DWDSE}}$是 diffusion weighted denoising score entropy对$x_0$的loss

他的表达式非常长...

$$\int_0^T\mathbb{E}_{x_t\thicksim p_{t|0}(\cdot|x_0)}\sum_{y\neq x_t}Q_t(x_t,y)\left(s_\theta(x_t,t)_y-\frac{p_{t|0}(y|x_0)}{p_{t|0}(x_t|x_0)}\log s_\theta(x_t,t)_y+K\left(\frac{p_{t|0}(y|x_0)}{p_{t|0}(x_t|x_0)}\right)\right)dt$$

但其实核心部分还是之前提到的，后面的K是normalizing constant用于保证L大于0


### Tweedie $\tau$ leaping

如果按照先前的设定ODE，我们可以给出denoiser:

$$p_{0|t}(x_0|x_t)=\left(\exp(-tQ)\left[\frac{p_t(i))}{p_t(x_t)}\right]_{i=1}^N\right)_{x_0}\exp(tQ)(x_t,x_0)$$

但问题是我们不能给出所有的ratio（论文中说只能给出Hamming距离为1的），但是我们之前有设定过$Q$作为中转站，此时我们不在试图一步直接跳到结果，而是采用一个更小的、离散的时间步长$\Delta t$，同时由于有$\tau$-leaping independence condition，我们假设咋在这个时间段内序列中的每个token是独立denoise的，此时的转移概率：

$$p^{\mathrm{tweedie}}(x_{t-\Delta t}^i|x_t) = \left(\exp(-\sigma_t^{\Delta t}Q)\mathbf{s}_\theta(\mathbf{x_t},\mathbf{t})_i\right)_{x_{t-\Delta t}^i}\cdot\exp(\sigma_t^{\Delta t}Q)(x_t^i,x_{t-\Delta t}^i)$$

where $\sigma_t^{\Delta t}=(\overline{\sigma}(t)-\overline{\sigma}(t-\Delta t))$



最终得到的算法：

<div align="center">
    <img src="/../../../../assets/pics/ai/dgm/ddm/ddm6.png" style="width: 100%;">
</div>

<div align="center">
    <img src="/../../../../assets/pics/ai/dgm/ddm/ddm7.png" style="width: 100%;">
</div>





