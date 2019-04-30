Demystifying Neural Style Transfer

作者：Yanghao Li, Naiyan Wang, Jiaying Liu, Xiaodi Hou

主题：（1）把NST当作域适应问题（domain adapation problem）；（2）系统的证明了拟合Gram矩阵等同于用二阶多项式核让Maximum Mean Discrepancy最小化

在NST中，风格信息本质上由一个CNN中激分布来表示(represented by the distribution of activations)

风格转移可以通过分布对齐（distribution alignment）来实现。

NST为特殊的domain adaptation problem

**亮点：**

将NST问题与Domain adaptation问题联系在了一起

1. First, we demonstrate that matching Gram matrices in
    neural style transfer [Gatys et al., 2016] can be reformulated as minimizing MMD with the second order polynomial kernel.
2. Second, we extend the original neural style transfer with
    different distribution alignment methods based on our
    novel interpretation.

Maximum Mean Discrepancy 最大平均差异