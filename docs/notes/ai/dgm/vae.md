---
statistics: true
---


# Variational Autoencoder(VAE)



## ELBO

在概率模型中，我们希望计算数据的边际似然（marginal likelihood），即在给定观测数据$x$的情况下，计算其在潜在变量$z$上的边际分布。

$$
p_{\theta}(x) = \int p(x, z) dz = \int p_{\theta}(x|z) p(z) dz
$$

但是：

- 这个积分往往是非常复杂甚至于不可解的
- $p(z)$是潜变量的分布，通常是不可观测（或者说很难得到正确的分布）

这时我们引入一个controllable的分布$q(x)$，