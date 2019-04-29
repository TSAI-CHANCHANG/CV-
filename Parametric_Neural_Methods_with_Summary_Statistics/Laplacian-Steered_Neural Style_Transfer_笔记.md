Laplacian-Steered Neural Style Transfer

作者：Shaohua Li, Xinxing Xu, Liqiang Nie, Tat-Seng Chua

主题：Gatys的方法简单来说时生成一个保留内容图片高级结构，运用style图片的低级结构渲染的图片。然而传统的优化目标会导致内容图片低层级特征丢失，而style图片的低层级特征却又主宰了新生成图片的低层级特征。为了解决这个问题，本文对传统优化目标进行了修正，加入了一个拉普拉斯loss。

拉普拉斯矩阵在计算机视觉中有着广泛应用，用于检测边缘和轮廓(contours)。

通过对比新生成图像与内容图片间Laplacian的差异，即可控制细节结构间的差异。

同时本文证明了Laplacian loss项不会影响图片风格化（content/style loss只上升了一点点）

亮点：可以在保证不影响图片风格化的同时，控制生成图片与content图片细节间的相似度，让生成图片能更好的保留content图片的细节

