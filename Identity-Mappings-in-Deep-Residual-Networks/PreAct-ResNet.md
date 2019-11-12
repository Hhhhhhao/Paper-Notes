# Identity Mappings in Deep Residual Networks

------

### Motivations

------

- 对resnet进行了更多的分析

- 提出了pre-act resnet， 训练过程更流畅(没有梯度消失的问题)

  ![fig1](/Users/leon/Projects/paper-notes/Identity-Mappings-in-Deep-Residual-Networks/images/1.jpg)

### Identity Mapping

------

###### Residual Units:

$$
\begin{aligned} \mathbf{y}_{l}=& h\left(\mathbf{x}_{l}\right)+\mathcal{F}\left(\mathbf{x}_{l}, \mathcal{W}_{l}\right) \\ & \mathbf{x}_{l+1}=f\left(\mathbf{y}_{l}\right) \end{aligned}
$$

在resnet原文中, $h(\cdot)$是一个identity mapping，$f(\cdot)$是ReLU

作者通过大量实验发现$h(x_l) = x_l$是resnet的最优选择，因为提供了==clean informaiton path==

###### Identity Mapping:

当$h$和$f$都为identity mapping时(注意原文中$f$是一个relu，不能算是identity mapping)：
$$
\mathbf{x}_{l+1}=\mathbf{x}_{l}+\mathcal{F}\left(\mathbf{x}_{l}, \mathcal{W}_{l}\right)
$$
递归可得：
$$
\mathbf{x}_{L}=\mathbf{x}_{l}+\sum_{i=l}^{L-1} \mathcal{F}\left(\mathbf{x}_{i}, \mathcal{W}_{i}\right)
$$
可以发现，对任意深或浅的网络$x_L$都可以表示为$x_l$的residual learning.

然而在plain network中，$x_L$是一系列矩阵转换$\prod_{i=0}^{L-1}W_ix_0$

对eq3求道可得：
$$
\frac{\partial \mathcal{E}}{\partial \mathbf{x}_{l}}=\frac{\partial \mathcal{E}}{\partial \mathbf{x}_{L}} \frac{\partial \mathbf{x}_{L}}{\partial \mathbf{x}_{l}}=\frac{\partial \mathcal{E}}{\partial \mathbf{x}_{L}}\left(1+\frac{\partial}{\partial \mathbf{x}_{l}} \sum_{i=l}^{L-1} \mathcal{F}\left(\mathbf{x}_{i}, \mathcal{W}_{i}\right)\right)
$$
发现梯度可以拆分为两部分，$\frac{\partial \mathcal{E}}{\partial \mathbf{x}_{L}}$直接传递$L$层的梯度至$l$层，另一项则是residual的梯度。同时eq4也说明了梯度在反传过程中不容易消失，因为$\frac{\partial \mathcal{E}}{\partial \mathbf{x}_{L}}$不能总为-1



### Other Skip Connections

------

###### Simplely scale the identity path: $h\left(\mathbf{x}_{l}\right)=\lambda_{l} \mathbf{x}_{l}$

$$
\mathbf{x}_{l+1}=\lambda_{l} \mathbf{x}_{l}+\mathcal{F}\left(\mathbf{x}_{l}, \mathcal{W}_{l}\right)
$$

递归得:
$$
\mathbf{x}_{L}=\left(\prod_{i=l}^{L-1} \lambda_{i}\right) \mathbf{x}_{l}+\sum_{i=l}^{L-1}\left(\prod_{j=i+1}^{L-1} \lambda_{j}\right) \mathcal{F}\left(\mathbf{x}_{i}, \mathcal{W}_{i}\right)
$$
反传为:
$$
\frac{\partial \mathcal{E}}{\partial \mathbf{x}_{l}}=\frac{\partial \mathcal{E}}{\partial \mathbf{x}_{L}}\left(\left(\prod_{i=l}^{L-1} \lambda_{i}\right)+\frac{\partial}{\partial \mathbf{x}_{l}} \sum_{i=l}^{L-1} \hat{\mathcal{F}}\left(\mathbf{x}_{i}, \mathcal{W}_{i}\right)\right)
$$
如果很多$\lambda_i > 1$的话则会有梯度爆炸，如果大多$\lambda_i < 1$则会引起梯度消失

###### Matrix Mapping:

如果$h$更复杂，则eq7中的第一项会变成$\prod_{i=l}^{L-1}h_i‘$ ，阻碍梯度传播



### Experiments on Skip Connections

------

![2](/Users/leon/Projects/paper-notes/Identity-Mappings-in-Deep-Residual-Networks/images/2.jpg)

![3](/Users/leon/Projects/paper-notes/Identity-Mappings-in-Deep-Residual-Networks/images/3.jpg)



从实验结果看出, shorcut有最好的效果，其他的方法都会影响梯度的传播.

Shorcut-only gating和1x1 conv应该cover了shorcut方法的解空间，但是通过反传找到这个解变得更加困难。

以上实验支持了之前的数学分析，但是之前把$f$也当作了identity mapping，事实中却是一个relu

$f$可以同过activation的放置顺序做成一个identity mapping



### Preactivation 

------

![4](/Users/leon/Projects/paper-notes/Identity-Mappings-in-Deep-Residual-Networks/images/4.jpg)

在resnet原来的设计中，$f$即影响shorcut path也影响residual path：
$$
\mathbf{y}_{l+1}=f\left(\mathbf{y}_{l}\right)+\mathcal{F}\left(f\left(\mathbf{y}_{l}\right), \mathcal{W}_{l+1}\right)
$$


如果把eq8变成asymmetric的形式(如下图):
$$
\mathbf{x}_{l+1}=\mathbf{x}_{l}+\mathcal{F}\left(\hat{f}\left(\mathbf{x}_{l}\right), \mathcal{W}_{l}\right)
$$


![5](/Users/leon/Projects/paper-notes/Identity-Mappings-in-Deep-Residual-Networks/images/5.jpg)

![6](/Users/leon/Projects/paper-notes/Identity-Mappings-in-Deep-Residual-Networks/images/6.jpg)



### Analysis

------

pre-activation主要有两个好处：

1.  Ease of Optimization： 如前面分析，当f是relu且有很多relu层时，eq3的approximation就不能很好的存在。当$f$是identity mapping时，梯度可以直接从$L$层传播到$l$层。
2. BN as pre-activation improves regularization