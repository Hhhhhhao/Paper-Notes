# Spectral Regularization for Combating Mode Collapse in GANs

### Abstract

------

本文基于SNGAN(Spectral Norm)提出了一种克服mode collapse的新方法：Spectral Regularization，并证明了Spectral Norm是Spectral Regularization的一种特殊情况。

Spectral Norm通过将判别器D里每层参数矩阵$W_i$的最大奇异值限定为1来达到Lipschitz continuity，然而作者通过分析Spectral Norm过后的参数矩阵$\bar{W}_{SN}$的spectral distribution（奇异值分布）发现了spectral collapse：当mode collapse发生时， spectral distribution变化剧烈，大量的奇异值随着训练剧烈变小。当这种情况发生时，GAN的表现也急剧下降(IS和FID)。作者因此推测mode collapse和spectral collapse通常同时发生。

因此提出spectral regularization来阻止spectral collapse进而减少mode collapse。具体做法是给参数矩阵的奇异值进行补偿, 使下降的奇异值等于最大奇异值，来控制spectral distribution在训练过程中尽量保持一致。



### Spectral Collapse

------

作者设置了一组SNGAN（最大奇异值是1），有不通的参数（batch size和卷积通道数）并观察他们discriminator每层的spectral distribution。

具体实验设置：

![fig3](/Users/leon/Projects/paper-notes/Spectral-Regularization-for-Combating-Mode-Collapse-in-GANs/images/fig3.jpg)

发现当mode collapse发生时，spectral distribution随训练过程变化剧烈：

![fig1](/Users/leon/Projects/paper-notes/Spectral-Regularization-for-Combating-Mode-Collapse-in-GANs/images/fig1.jpg)



而mode collapse没有发生时， spectral distribution随训练保持一致。

![fig2](/Users/leon/Projects/paper-notes/Spectral-Regularization-for-Combating-Mode-Collapse-in-GANs/images/fig2.jpg)

对每个组也进行了分析：

![fig4](/Users/leon/Projects/paper-notes/Spectral-Regularization-for-Combating-Mode-Collapse-in-GANs/images/fig4.jpg)

从实验结果来看，大通道数的模型更少发生mode collapse和spectral collapse; 而大的batch size和较少的通道数易引发mode collapse和spectral collapse。



### Spectral Regularization Algorithm

------

主要思想是对发生变化的spectral distrbuition进行一定的补偿，使其和未发生变化前相似

具体做法如下

如果对参数矩阵$W$进行奇异值分解得：
$$
W=U \cdot \Sigma \cdot V^{T}
$$
其中$\Sigma=\left[\begin{array}{ll}{D} & {0} \\ {0} & {0}\end{array}\right]$,  $D=\operatorname{diag}\left\{\sigma_{1}, \sigma_{2}, \cdots, \sigma_{r}\right\}$

设置$\Delta D = \operatorname{diag}\left\{\sigma_{1}-\sigma_{1}, \sigma_{1}-\sigma_{2}, \cdots, \sigma_{1}-\sigma_{i}, 0, \cdots, 0\right\}$去补偿$D$, 其中$i$是超参数

由$D^{'} = D + \Delta D$ 得出补偿过后的$W'= W + \Delta W$ 其中 $\Delta W$为：
$$
\Delta W=U \cdot\left[\begin{array}{cc}{\Delta D} & {0} \\ {0} & {0}\end{array}\right] \cdot V^{T}=\sum_{k=2}^{i}\left(\sigma_{1}-\sigma_{k}\right) u_{k} v_{k}^{T}
$$
最后对$W'$进行谱归一化得到Spectral Regularization：
$$
\bar{W}_{\mathrm{SR}}(W)=\frac{W+\Delta W}{\sigma(W)}=\bar{W}_{\mathrm{SN}}(W)+\Delta W / \sigma(W)
$$
可以发现$i=1$时$\bar{W}_{SR} = \bar{W}_{SN}$



### Spectral Regularization Gradients

------

![fig5](/Users/leon/Projects/paper-notes/Spectral-Regularization-for-Combating-Mode-Collapse-in-GANs/images/fig5.jpg)

前两项即SN的gradient项， SN通过对最大奇异值进行正则化防止该层变化对单一方向变得敏感，因此减少mode collapse。

但是从本文实验中可以发现，由于spectral collapse，即使加入了SN，该层变化还是会倾向于单一方向，引发mode collapse。因此SR加入了另外两项：第三项与第二项相同，加强了对最大奇异值正则化的效果；第四项则鼓励该层变换朝向所有方向$u_k v_k^T$且weighted by与最大项间的差距。



### Expeerimental Detail

------

在各个setting上对比了SN和SR，SR可以稳定提升IS和FID，限制spectral distribution的变化

![fig6](/Users/leon/Projects/paper-notes/Spectral-Regularization-for-Combating-Mode-Collapse-in-GANs/images/fig7.jpg)

![fig8](/Users/leon/Projects/paper-notes/Spectral-Regularization-for-Combating-Mode-Collapse-in-GANs/images/fig8.jpg)



### Thoughts

------

1. 从结果的分析上来看，SR确实可以有效的控制spectral distrbution的变化从而减少mode collapse
2. 不过从算法上来看，应用SR可能涉及到对每层conv进行SVD分解，引入较大的计算量
3. 本文只在cifar10和stl10上进行了实验，不知道在面对更真实的数据集如imagenet或celeba时效果如何
4. 超参数i直接影响模型performance，感觉i也比较难控制。且SR过后spectral distribution过于僵硬，不知道是不是一个好现象...