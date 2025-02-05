---
comments: true
statistics: true
---





# Deep Generative Models

!!! abstract
    生成式模型被广泛应用于图像生成、文本生成、语音生成等领域，我在25年初（寒假）开始进行这部分的学习，主要参考的是[Stanford CS236](https://deepgenerativemodels.github.io/syllabus.html) 课程和[MIT 6.S987](https://mit-6s978.github.io/schedule.html) 课程的讲义还有[我表哥的自学笔记](https://zhuanlan.zhihu.com/p/631001372)(这个强烈推荐，很高完成度的写了生成模型的概述，包含了截止23年的几乎所有深度生成模型)

    进行这部分学习的初衷是在侯老师的实验室，我在秦睿师兄的指导下开展关于蛋白质分子生成工作的评测，由于不想局限于使用现有模型而是能更深入的理解模型的工作原理，因此开始进行这部分的学习。

    - [x] [VAE](./vae.md)
    - [ ] [Normalizing Flows](./nf.md)
    - [ ] [Autoregressive Models](./am.md)
    - [ ] [Ganerative Adversarial Networks](./gan.md)
    - [ ] [Diffusion Models](./dm.md)


## Intro

我们说生成式模型（Generative Model），与之相对的是判别式模型（Discriminative Model）：

- Discriminative：

    sample $x \rightarrow$ label $y$

    只有一个期望输出

- Generative：

    label $y \rightarrow$ sample $x$

    有多个期望输出

???+ example "x and y"

    - 在chatbot中，$y$是prompt，$x$是response
    - 在蛋白质生成中，$y$是性质/约束，$x$是蛋白质结构


![](../../assets/pics/ai/dgm/dgm1.png)

并且利用贝叶斯公式我们可以由生成式模型的概率得到判别式：

$$
p(y|x) = \frac{p(x|y)p(y)}{p(x)}
$$

但是反之并不能很好的成立：

<div style="text-align: center;">
    <img src="/../../../../assets/pics/ai/dgm/dgm2.png" style="width: 80%;">
</div>

因为我们需要知道数据sample的分布，但是只有生成式概率$p(x|y)$，而$p(x)$是不知道的。

!!! note "总结"

    生成式模型就是要去寻找数据的潜在分布$p(x)$，来生成与真实数据分布相似的样本

## Probabilistic Modeling

前文中我们提到了一系列概率$p$，但是这些概率是哪里来的呢？

!!! Tip "Hint"
    <div style="text-align: center;">
        <span style="font-size: 1.5em;">
            Probability is part of the modeling.
        </span>
    </div>

怎么理解这句话呢，我的理解就是我们在学习时，其实就是在学习一种概率分布，对观测的数据进行建模，所以其实得到的分布函数就是我们的模型。

这样说有点抽象，试着举个例子：

我们以生成式模型为例，采用概率建模的方法：

???+ example "Image Generation"

    - 我们的目标是通过给定的一些图像，生成一个新的图像

    ![](../../assets/pics/ai/dgm/dgm3.png)
    
    - 在通过一系列的方法，得到一个估计（estimated）的分布，这个分布的评估是通过损失函数$L$来进行的
  
    ![](../../assets/pics/ai/dgm/dgm4.png)

    - 此时我们依照给定特征$y$，生成一个图像$x'$，这个$x'$就遵从我们得到的分布$p(x|y)$

    ![](../../assets/pics/ai/dgm/dgm5.png)

!!! note "Notes"
    
    - Generative models involve statistical models which are often designed and derived by humans.
    - Probabilistic modeling is not just work of neural nets.
    - Probabilistic modeling is a popular way, but not the only way.




## "Deep" Generative Models

深度学习是一种表征学习，也就是说我们学习的是如何将数据 $x$ 映射到$f(x)$，使得损失函数$L(f(x), y)$最小。

在深度生成模型的学习中，我们学习的是如何表征概率分布

<div style="text-align: center;">
    <img src="/../../../../assets/pics/ai/dgm/dgm6.png" style="width: 80%;">
</div>

这里我们学习得到一个简单分布到复杂分布的映射，这个映射就是我们的模型。

$$
 \pi \rightarrow g(\pi)
$$

像这样：

<div style="text-align: center;">
    <img src="/../../../../assets/pics/ai/dgm/dgm7.png" style="width: 80%;">
</div>

随后最小化基于数据的损失函数$L(p_x , g(\pi))$，得到模型$g$

!!! note "总结"

    一个DGM可能包括：

    - Formulation：
        - formulate a problem as a probabilistic modeling （进行概率建模）
        - decompose a complex distribution into simpler and tractable ones （将复杂分布分解）
    - Representation：deep neural networks to represent data and their distributions （使用深度神经网络表示数据和他们的分布）
    - Objective function：evaluate the predicted distribution
    - Optimization：optimize the networks and/or the decomposition
    - Inference:
        - sampler 
        - probability density estimator
        - ...

## Latent Variable Models

在这里我觉得需要提前说明的是，在深度生成模型中，我们经常使用隐变量（Latent Variables）

例如这样一组图片：

<div style="text-align: center;">
    <img src="/../../../../assets/pics/ai/dgm/dgm8.png" style="width: 50%;">
</div>

由于性别、年龄、肤色等等因素，图像$x$存在多种可能的变化，但除非图片是被注释annotated的，否则我们无法得知这些因素，或者说，这些因素是不可见的（not explicitly available）。

!!! tip "Latent Variable"
    这时我们的想法就是用隐变量$z$显示的建模来表示这些因素。


很直观的想法是用 Bayes 网络来表达：

<div style="text-align: center;">
    <img src="/../../../../assets/pics/ai/dgm/dgm9.png" style="width: 80%;">
</div>

但是这其中的条件概率分布是很难得到的，因此我们使用神经网络来近似这个条件概率分布：

<div style="text-align: center;">
    <img src="/../../../../assets/pics/ai/dgm/dgm10.png" style="width: 80%;">
</div>  

这时我们通常假设$z$是服从某种简单分布的，例如高斯分布：$z \sim N(0, 1)$

通过神经网络的处理，我们可以得到$p(x|z)=N(x|\mu_{\theta}(z), \sigma_{\theta}(z))$，在这里$\theta$是神经网络的参数。

<div style="text-align: center;">
    <img src="/../../../../assets/pics/ai/dgm/dgm11.png" style="width: 60%;">
</div>

我们希望在训练结束后，$z$可以表示$x$的潜在因素（特征）

使用$z$来表示有两点原因：

1. 用潜变量可以简化问题
2. 得到的潜变量本身就具有他的意义（不用在生成，用在推断或寻找特征）


## Measure Distribution

在得到生成分布后，我们需要评估这个分布的优劣，这里对几个常用的评估方法进行介绍。

### KL divergence

KL 散度（Kullback-Leibler divergence），用于衡量两个分布之间的差异，对于给定的两个分布$p$和$q$，KL散度定义为：

离散的：

$$
D_{KL}(p||q) = \sum_{x \in \mathcal{X}} p(x) \log \frac{p(x)}{q(x)}
$$

连续的：

$$
D_{KL}(p||q) = \int p(x) \log \frac{p(x)}{q(x)} dx
$$

- 非负性：$D_{KL}(p||q) \geq 0$

- KL散度越小，两个分布越相似。

- KL散度不具有对称性，即$D_{KL}(p||q) \neq D_{KL}(q||p)$



在对$p_{\theta}$和$p_{data}$进行比较时，我们取这两个变量的KL散度最小值：

$$
\min_{\theta} D_{KL}(p_{data}||p_{\theta})
$$

可以推导KL散度最小值等价于最大化 ==期望对数似然==（Maximum Log-Likelihood Estimation）：（用离散的KL公式，打开$\log$，其中$p_{data}(x)$是常数，所以可以忽略）

$$
arg\min_{\theta} D_{KL}(p_{data}||p_{\theta}) = arg\max_{\theta} \mathbb{E}_{x \sim p_{data}} \log p_{\theta}(x)
$$

!!! warning "缺陷"

    - 由于我们忽略了$p_{data}(x)$的期望项，所以最终我们只能得到一个参数$\theta$的取值的估计，但是不能知道how close the model is to the true distribution。
    - In practice, we can't compute the true distribution $p_{data}$


对于第二个问题，我们为了避免涉及到$p_{data}$，我们使用另一种似然方法：==经验对数似然==（Empirical Log-Likelihood）

期望对数似然是对所有数据点的对数似然求期望，而经验对数似然是对所有数据点的对数似然求平均。

$$
\max_{\theta} \mathbb{E}_{x \sim p_{data}} \log p_{\theta}(x) \approx \max_{p_{\theta}} \frac{1}{N} \sum_{i=1}^{N} \log p_{\theta}(x_i)
$$

这样我们就得到了所需要的最大似然估计。

对于数据的最大化似然：（联合概率似然）

$$
p_{\theta}(x^{1}, x^{2}, ..., x^{N}) = \prod_{i=1}^{N} p_{\theta}(x^{i})
$$







