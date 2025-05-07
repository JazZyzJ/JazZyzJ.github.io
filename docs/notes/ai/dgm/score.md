---
comment: true
---


# Score-based Models

<head>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.16.7/katex.min.js"
            integrity="sha512-EKW5YvKU3hpyyOcN6jQnAxO/L8gts+YdYV6Yymtl8pk9YlYFtqJgihORuRoBXK8/cOIlappdU6Ms8KdK6yBCgA=="
            crossorigin="anonymous" referrerpolicy="no-referrer">
    </script>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/pseudocode@latest/build/pseudocode.min.css">
    <script src="https://cdn.jsdelivr.net/npm/pseudocode@latest/build/pseudocode.min.js">
    </script>
</head>

## Intro

在上一个模型[Energy-based Models](./energy.md)中，我们通过构建score matching来训练EBMs，好处就是我们不再需要关心pdf是否是标准化的（因为不需要计算partition function），只需要考虑梯度：


<div align="center" > 
    <img src="/../../../../assets/pics/ai/dgm/sc/ebm.gif" style="width: 40%;">
    <img src="/../../../../assets/pics/ai/dgm/sc/score.gif" style="width: 40%;">
    </div>

可以看到右边的梯度曲线，无需考虑面积问题，我们不再使用density来衡量模型，而是使用score

但是可以看到Fisher divergence是一个定义好的函数，不局限在EBM的训练中，所以我们可以将EBM的训练推广到score-based models中

在score-based models中，我们不再考虑一个能量（或者说在EBM的训练目标中$f_\theta$是一个标量函数，类似于势能），而是直接构建一个vector field of gradient，这时我们的对象就不仅是标量函数了，而是arbitrary vector fields


<div align="center" > 
    <img src="/../../../../assets/pics/ai/dgm/sc/sc1.png" style="width: 80%;">
    </div>

现在我们来重申一下score-based models的任务：

- **Given**: i.i.d. samples${x_1, x_2, \cdots, x_n} \sim p_{data}(x)$
- **Task**: Estimating the score $\nabla \log p_{data}(x)$
- **Score Model**: A learnable vector-valued function $s_\theta(x)$
- **Goal**: $s_\theta(x) \approx \nabla \log p_{data}(x)$

也就是

$$
\begin{aligned}
&\min_\theta \frac{1}{2} \mathbb{E}_{x \sim p_{data}} \| \nabla_{x} \log p_{data}(x) - s_\theta(x) \|_{2}^2 \\
&=\min_{\theta} E_{\mathbf{x} \sim p_{\mathrm{data}}}[\frac{1}{2}\left\|\mathbf{s}_\theta (\mathbf{x})\right\|_2^2+\operatorname{tr}(\underbrace{\nabla_{\mathbf{x}}^2 \mathbf{s}_\theta(\mathbf{x})}_{\text {Jacobian of } \mathbf{s}_\theta(\mathbf{x})})]
\end{aligned}
$$

但是这个式子中的雅各比矩阵的trace很难计算

!!! warning "Not Scalable!"
    <div align="center" > 
    <img src="/../../../../assets/pics/ai/dgm/sc/sc2.png" style="width: 80%;">
    </div>

所以我们需要接下来的方法进行优化

## Denoising Score Mathching 

总体的思想是：我们不再对原先的$p_{data}$进行估计，而是对原始数据加入噪声后，对加入噪声后的数据$\tilde{x}$进行估计

设置噪声：

给定原始数据分布 $p_{data}(x)$，加噪后的数据$\tilde{x}$通过以下条件分布生成：

$$
q_{\sigma}(\tilde{x} | x) = \mathcal{N}(x; \tilde{x}, \sigma^2 I)
$$

即$\tilde{x} = x + \delta z, z \sim \mathcal{N}(0, I)$

<div align="center" > 
    <img src="/../../../../assets/pics/ai/dgm/sc/sc3.png" style="width: 100%;">
    </div>

扰动后的边际分布是原始分布与噪声的卷积：

$$
p_{\sigma}(\tilde{x}) = \int q_{\sigma}(\tilde{x} | x) p_{data}(x) d\tilde{x}
$$

现在我们来看score matching的优化目标，进行化简得到：

<div align="center" > 
    <img src="/../../../../assets/pics/ai/dgm/sc/sc4.png" style="width: 80%;">
    </div>

??? quote "Prove"
    
    === "unfold"

        <div align="center" > 
        <img src="/../../../../assets/pics/ai/dgm/sc/sc5.png" style="width: 80%;">
        </div>

    === "simplify the tricky part"

        <div align="center" > 
        <img src="/../../../../assets/pics/ai/dgm/sc/sc6.png" style="width: 80%;">
        </div>

        这里的化简就是将$q_{\sigma}(\tilde{x})$展开，然后利用他的线性性将gradient移到里面，随后重写这一项（加个log），方便将积分变为关于两个变量的期望
    
    === "final"

        <div align="center" > 
        <img src="/../../../../assets/pics/ai/dgm/sc/sc7.png" style="width: 90%;">
        </div>
    
因为我们取q是Gaussian的，所以现在就有$\nabla_{\tilde{x}} \log q_{\sigma}(\tilde{x} | x) = \frac{\tilde{x} - x}{\sigma^2}$

- **Pros**: efficient for high-dimensional data
- **Cons**: cannot estimate the score of clean(original) data 

回顾一下整个流程，我们将score matching的优化目标进行化简，得到（高斯噪声下的）denoising 形式

<div align="center" > 
    <img src="/../../../../assets/pics/ai/dgm/sc/sc8.png" style="width: 100%;">
    </div>

如何理解这个denoising呢？

我的理解就是在不知道$p_{data}$的情况下，尽量让分数间接匹配原始数据分数，可以看到公式中的$s_\theta$就是要在$\delta \to 0$时逼近$\nabla \log q_{\sigma}(\tilde{x} | x)$

~~草 我无法说服自己了~~

换个角度考虑，我们在思考optimal training object的时候，会发现上面我说的是成立的（也就是“尽量”）的意思，就是指怎么让未化简的公式和化简后的公式从逻辑上等价（因为化简前score是需要逼近扰动后的数据梯度，但化简后score看起来逼近的是噪声的梯度）

这里引入一个定理

!!! tip "Tweedie's formula"
    
    **Optimal denoising strategy is to follow the gradient:**
    
    $$
    \begin{aligned}
    E_{\mathbf{x} \sim p(\mathbf{x} \mid \tilde{\mathbf{x}})}[\mathbf{x}] & =\tilde{\mathbf{x}}+\sigma^2 \nabla_{\mathbf{x}} \log q_\sigma(\tilde{\mathbf{x}}) \\
    & \approx \tilde{\mathbf{x}}+\sigma^2 \mathbf{s}_\theta(\tilde{\mathbf{x}})
    \end{aligned}
    $$

    就是说，给出加噪后的图像，清晰图像的预期值符合上面的公式

## Sliced Score Matching


对于高维数据进行score matching时，计算雅各比矩阵的trace非常困难，所以需要进行近似，在这里我们想到一种方式将高维数据降维，通过随机投影（projection）


<div align="center" > 
    <img src="/../../../../assets/pics/ai/dgm/sc/sc9.png" style="width: 80%;">
    </div>

我们假设将两个分数（真实和预测）投影到随机向量$v$上后如果他们相等，那么就认为两个分数相等

$$
\begin{aligned}
&\frac{1}{2} E_{\mathbf{v} \sim p_{\mathbf{v}}} E_{\mathbf{x} \sim p_{\text {data }}}\left[\left(\mathbf{v}^{\top} \nabla_{\mathbf{x}} \log p_{\text {data }}(\mathbf{x})-\mathbf{v}^{\top} \mathbf{s}_\theta(\mathbf{x})\right)^2\right]\\
&\text { Integration by parts :}\\
&E_{\mathbf{v} \sim p_{\mathbf{v}}} E_{\mathbf{x} \sim p_{\text {data }}}\left[\mathbf{v}^{\top} \nabla_{\mathbf{x}} \mathbf{s}_\theta(\mathbf{x}) \mathbf{v}+\frac{1}{2}\left(\mathbf{v}^{\top} \mathbf{s}_\theta(\mathbf{x})\right)^2\right]
\end{aligned}
$$

这里的$\mathbf{v^T}$与$\mathbf{s_\theta}$的乘积（点乘）是一个标量，所以最终我们得到的结果是一维的，通常我们随机选取$v$，可以从$p_v$（Gaussian、Rademacher中采样）中采样



现在来解释一下为什么计算这里的$\mathbf{v^T \nabla_{x} s_\theta \mathbf{v}}$是scalable的

!!! tip 
    <div align="center" > 
    <img src="/../../../../assets/pics/ai/dgm/sc/slice.gif" style="width: 80%;">
    </div>

    - 第一步前向传播计算$\mathbf{s_\theta}$
    - 第二步通过投影将$\mathbf{s_\theta}$降维到一维
    - 通过一次反向传播来计算这个标量对于所有输入的梯度
    - 再取一个点积将这个梯度降到一维



## Inference

我们知道可以按照分数的梯度方向进行采样，但是那样数据分布会完全收敛到一个单点，所以需要引入噪声

采用Langevin dynamics MCMC进行推理：

$$
\tilde{\mathbf{x}}_{t+1} \leftarrow \tilde{\mathbf{x}}_t+\frac{\epsilon}{2} \mathrm{~S}_\theta\left(\tilde{\mathbf{x}}_t\right)+\sqrt{\epsilon} \mathbf{z}_t
$$

但是这种naive的方式效果非常差，原因有以下几点：

- Manifold hypothesis: 数据分布在低维流形上，当数据逐渐收敛时，我们的分数会失去定义

    <div align="center" > 
    <img src="/../../../../assets/pics/ai/dgm/sc/sc10.png" style="width: 80%;">
    </div>

    可以看到即使PCA降维后，样本仍没有太多变化

- 数据密度在不同区域不同，会在低数据密度区域迷失方向

<div align="center" > 
    <img src="/../../../../assets/pics/ai/dgm/sc/sc11.png" style="width: 80%;">
    </div>

这一点比较好理解，我的理解就是在低密度数据区域，数据的分布很稀疏，梯度方向不稳定，很难收敛到高数据密度区域，造成采样失败


- 混合密度分布的比例系数会消失

<div align="center" > 
    <img src="/../../../../assets/pics/ai/dgm/sc/sc12.png" style="width: 80%;">
    </div>

<div align="center" > 
    <img src="/../../../../assets/pics/ai/dgm/sc/sc13.png" style="width: 80%;">
    </div>




<script>
    pseudocode.renderClass("pseudocode");
</script>