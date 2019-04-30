Stable and Controllable Neural Texture Synthesis and style Transfer Using Histogram Losses

作者：Eric Risser, Pierre Wilmot, Connelly Barnes

主题：

相较于Gatys的方法，本文的方法只需要45.7%的时间即可完成。

在运用神经网络生成的方法中的不稳定性：时常需要针对每个图片进行参数微调以得到高质量结果。

生成过程随着输出尺寸的增加而变得更不稳定。该不稳定性可由novel histogram loss项来抑制。该项能提高质量，减少残影（ghosting），还能加速收敛（convergence）

进一步提升：localized losses

**亮点：**

通过加入histogram losses使得Gatys等人以及后续论文中所使用的loss function具有更好的稳定性和质量，能更好的约束材质统计数据的分散性。