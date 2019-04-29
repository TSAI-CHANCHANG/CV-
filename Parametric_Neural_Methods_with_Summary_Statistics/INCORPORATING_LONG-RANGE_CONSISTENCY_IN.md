INCORPORATING LONG-RANGE CONSISTENCY IN CNN-BASED TEXTURE GENERATION

作者：Guillaume Berger & Roland Memisevic

主题：Gatys的方法大多数情况下能产生很好的结果，但是由于没有考虑non-local结构，无法生成具有长距离间相关的图片，（例：规则砖墙材质）

本文的贡献是介绍一种可以处理长距离结构的方法。该法使用了一种基于product的材质特征的简单修改以处理远程结构的方法

在high-level features假设Markov-structure，这使得我们可以跨点，而不是局限于局部。

提出了一种方法来满足图像生成中的长程一致性约束。 展示了该方法允许生成具有各种全局对称属性的纹理，并且可以将纹理生成应用于绘画中。

亮点：修正了Gatys法在面对根据一个规则纹理生成新纹理时表现不佳的问题。