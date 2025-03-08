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


## Build


z和x有相同的维度






