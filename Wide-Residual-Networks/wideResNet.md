## Wide Residual Networks



### Motivations

------

之前resnet的文章都关注于网络深度的研究，这篇文章则关注网络的宽度。

作者称，resnet中的identity mapping允许梯度直接从$L$层传播到$l$层的有优点也正是它的缺点。在很深的resnet中，网络没有对与residual path没有任何约束. residual path从而不一定会学习到有用的信息。在很深的网络中，很可能只有一些residual path是有用的，而其他的只能提供很少的信息，是冗余的。这大概也是resnet1000会overfit的原因。



### Wide Residual Networks

------

在BN-RELU-Conv的pre activation resnet中，residual unit可表示为：
$$
\mathbf{x}_{l+1}=\mathbf{x}_{l}+\mathcal{F}\left(\mathbf{x}_{l}, \mathcal{W}_{l}\right)
$$
作者设置了$l$ (depth) 和 $k$ (width) 两个参数来研究resnet

![1](/Users/leon/Projects/paper-notes/Wide-Residual-Networks/image/1jpg.jpg)

同时，作者也设置了一组resnet block内的kernel size的设计来研究kernel size减小到1是否对resnet有影响

![2](/Users/leon/Projects/paper-notes/Wide-Residual-Networks/image/2.jpg)

作者用WRN-n-k来表示一个n层k宽的resnet

