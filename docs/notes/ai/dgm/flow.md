---
comment: true
---


# Normalizing Flow Models

>在进行VAE的学习时我们引入了潜变量对样本进行编码，但是VAE缺乏一个tractable的likelihood，因此我们使用了变分推断来近似，那有没有什么方式能让我们使用潜空间来表示样本，并且有tractable的likelihood呢？这就是Normalizing Flow Model的用武之地。

!!! tip "Key"
    Flow Model的本质是使用一个可逆的、光滑的变换，将简单分布转换为复杂分布。

回顾一下VAE的思路，我们用一个神经网络来训练

$$
p(x|z) = \mathcal{N}(\mu_{\theta}(z),\Sigma_{\theta}(z))
$$

但是需要enumerate所有可能的z❗️


而Flow Model的思路就是利用可逆变换来确定到底哪些z是与我们的x相关的，并且这样还能确定唯一的对应关系:

$$
\text{invert } p(x|z) \rightarrow p(z|x)
$$

$$
\text{by finding a function } x = f_{\theta}(z) 
$$

但是这样我们就需要保证z和x有相同的维度并且连续（在VAE中通过encoder压缩了z的维度，现在不行了）




## Build

??? note "Recap on Probability"

    先让我们 ~~预习~~ 回忆一些概率论的知识：

    首先是分布函数和密度函数在以后会用CDF（cumulative distribution function）和PDF（probability density function）来表示。

    哦对，对于联合分布随机向量$X$，如果他是高斯分布，他的密度函数可以表示为：

    $$
    p(x) = \frac{1}{\sqrt{(2\pi)^n|\Sigma|}} e^{-\frac{1}{2}(x-\mu)^T\Sigma^{-1}(x-\mu)}
    $$



    对于一个可逆变化$f$，作用到随机变量$Z$后，$X=f(Z)$密度函数会变成：

    $$
    p_{X}(x) = p_{Z}(f^{-1}(x)) \left| det \frac{\partial f^{-1}(x)}{\partial x} \right|
    $$

    对于一个可逆矩阵$A$，有$det(A^{-1})=det(A)^{-1}$（因为$det(M) det(M^{-1}) = det(I) = 1$）

    就有：

    $$
    p_{Z}(z) = p_{X}(f(z)) \left| det \frac{\partial f(z)}{\partial z} \right| ^{-1}
    $$

    也就是求算反函数雅各比矩阵的行列式转变为了求算正函数雅各比矩阵的行列式的倒数。

!!! tip "Idea"
    在对一个连续分布进行可逆变换时，如果这是一个线性变换，那么这个过程可以理解为对度量单位进行变换（米->英尺），但是显然对单位进行变换并不会让数据变得简单，但是如果我们做的是非线性变换，那么将是从不同角度审视数据（改变坐标系），让建模变得简单，并且由于是可逆的，所以不会丢失信息。

现在我们来正式的定义一个Flow Model：

!!! note "Flow Model"

    - In a **normalizing flow model**, the mapping between $Z$ and $X$, given by $f_{\theta}$: $\mathbb{R}^n \rightarrow \mathbb{R}^n$ is **deterministic** and **invertible** such that:

    <div style="text-align: center;">
        <img src="/../../../../assets/pics/ai/dgm/flow/f1.png" style="width: 20%;">
        </div>
    
    - by using change of variables, the marginal likelihood $p(x)$ is:

    $$
    p_X(x,\theta) = p_Z(f_{\theta}^{-1}(x)) \left| det \frac{\partial f_{\theta}^{-1}(x)}{\partial x} \right|
    $$

    - $x,z$连续且维数相同

我们的方法是施加一些列的可逆变换，将简单分布转化为复杂的：

<div style="text-align: center;">
    <img src="/../../../../assets/pics/ai/dgm/flow/f2.png" style="width: 70%;">
    </div>

可以这样表示

$$
\begin{align}
z_K &= f_{\theta_K} \circ \dots \circ f_{\theta_0}(z_0) \\ &= f_{\theta_K}(f_{\theta_{K-1}}(\dots f_{\theta_0}(z_0))) 
\end{align}
$$

- 从一个简单分布$z_0$开始，应用一系列的可逆变换，我没有省略$\theta$的脚注，因为每一个函数的训练参数都是不同的

接下来看一下最后的$p_X(x)$怎么推导：

对Flow过程的第$i$个函数，有：

$$
\begin{align}
z_{i-1} &\sim p_{i-1}(z_{i-1}) \\
z_i &= f_{i}(z_{i-1}),\text{ thus }z_{i-1} = f_{i}^{-1}(z_i) \\ \\
\text{then using change of variables formula:} \\
p_i(z_i) &= p_{i-1}(f_{i}^{-1}(z_i)) \left| det \frac{\partial f_{i}^{-1}(z_i)}{\partial z_i} \right| \\
&= p_{i-1}(z_{i-1}) {\left| det \frac{\partial f_{i}(z_{i-1})}{\partial z_{i-1}} \right|}^{-1} \\
\end{align}
$$

对等号两边取对数：

$$
\log p_i(z_i) = \log p_{i-1}(z_{i-1}) - \log \left| det \frac{\partial f_{i}(z_{i-1})}{\partial z_{i-1}} \right|
$$

最后对整个过程累加：

$$
\begin{aligned}
\log p(\mathbf{x})=\log f_K\left(\mathbf{z}_K\right) & =\log f_{K-1}\left(\mathbf{z}_{K-1}\right)-\log \left|\operatorname{det} \frac{d f_K}{d \mathbf{z}_{K-1}}\right| \\
& =\log f_{K-2}\left(\mathbf{z}_{K-2}\right)-\log \left|\operatorname{det} \frac{d f_{K-1}}{d \mathbf{z}_{K-2}}\right|-\log \left|\operatorname{det} \frac{d f_K}{d \mathbf{z}_{K-1}}\right| \\
& =\ldots \\
& =\log f_0\left(\mathbf{z}_0\right)-\sum_{i=1}^K \log \left|\operatorname{det} \frac{d f_i}{d \mathbf{z}_{i-1}}\right|
\end{aligned}
$$

这样一个path就是一个flow，如果我们将$f$替换为连续的分布$\pi_i$，那么我们得到的就是一个normalized flow

需要注意的是：

- $f_i$的选择是easily invertable的（因为我们要对其进行训练，所以无论我们怎么改变$\theta_i$，都要保证$f_i$是可逆的）
- 雅各比矩阵的行列式要易于计算






## References

[Lil'Log](https://lilianweng.github.io/posts/2018-10-13-flow-models/)