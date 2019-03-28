#### 3 Neural Style Transfer 的演化

如何对style进行建模并且将其从图像中抽取出来是非常重要的。最直接的方法就是把 *视觉风格建模* 回退到成熟的 *视觉材质建模* 上。

第二大问题则是如何使用给定风格信息在保留图片内容的情况下重建图片，该技术被称为**the Image Reconstruction**

本节将讲述NST的推导与基础。

##### 3.1 Visual Texture Modelling

###### 带统计摘要的参数化纹理建模

纹理建模的一条途径是从样本纹理中捕获图像统计信息，并利用摘要统计属性来对纹理进行建模。

Gram-base表示法是设计用来捕捉全局统计数据并抛出空间排布方式的，因此不适合用于对规律性长距离对称texture结构建模。

###### 非参数化纹理建模

假定在材质图片中，每个像素都完全由其空间上的邻居定义性质。

古老方案：在源材质图片上搜寻相似的相邻区间然后赋值给相应的像素。

##### 3.2 Image Reconstruction

从输入图片中提取出抽象表示乃许多视觉任务重要的一步。本文重点是**基于CNN表述的图像重建算法**。

###### 1) Image-Optimisation-Based Online Image Reconstruction

从一个随机噪声图片，通过梯度下降法逐渐优化成一个拥有期望CNN表示的图片

###### 2) Model-Optimisation-Based Offline Image Reconstruction

提前训练一个前馈神经网络。

#### 4. NST算法的分类

由于style的定义十分主观，因此此处只关注笔刷的细节、语义、深度与种类。审美评价标准会放在第8章。

算法根据分类分成两种，一种通过迭代优化图片，一种离线优化生成模型，然后使用此模型单路生成符合风格的图片。

##### 4.1 Image-Optimisation-Based Online Neural Methods

建模并提取风格与内容图片的信息，将他们合并成目标representation，再迭代重建风格化结果。

一般不同的IOB-NST算法是共享同一个IOB-IR（图片重建）技术，但是对于视觉风格的建模千差万别。

共同缺陷就是计算代价极大。

###### 4.1.1 Parametric Neural Methods with Summary Statistics

style在此方法下被描述为空间统计数据的总结。

**Grma法**

I∗ = arg min Ltotal(Ic, Is, I) = arg min αLc(Ic, I) + βLs(Is, I), 也就是尽量减少生成图片的内容与内容图片的loss，以及减少生成图片的风格与风格图片的loss。α β用于调节对风格和内容的重视度。Lc内容loss用特征图像Ic在第l层特征表示Fl平方欧几里得距离定义

结合了多层数据是成功的关键。拥有更连续和更顺滑的风格化。

相反，通过匹配网络的较高层上的内容，可以改变精细结构以与期望的样式一致，同时保留内容图像的内容信息。

Gatys et al. 他和算法不需要ground truth data，也没有对图片的风格有限制。

<u>由于Grma表示法的缺陷，无法进行真实感生成。该法也没有考虑笔刷的多样化以及语义和其深度信息</u>，而这些在内容image中是重要的组成部分。

还有其他的以统计方式编码风格信息的方法。（从Gram法派生而来）

**domain adaption**，目标是逐步修正一个建立在被标记训练数据的源domain上的建模，能够预测一个来自未标记测试数据domain的数据的标签。

domain adaption 的一种手段是比对源domain的一个例子与target domain的一个例子，并最小化他们之间的distribution差异，Maximum Mean Discrepancy (MMD)是一种流行的选择，用于衡量两个distrubution间的差别

比对用Gram法表示的风格表示实质上等于将一个有二次多项式形式的MMD最小化。

另一些表示法有batch normalisation统计表示法

<u>Grma法还有个缺陷就是在优化过程中的不稳定性。</u>

导入直方图loss

Risser让优化比对直方图loss，实现了一个更平稳的风格转换器，只需更少的迭代和参数微调代价。但缺点就是高计算复杂度。但对于画面深度以及细节间的关联的考量缺陷仍然存在。

###### 4.1.2 Non-parametric Neural Methods with MRFs

在内容照片和style照片的角度与形状相似的情况下，表现效果良好，但是当两者有很大不同时就无法好好运作，而且在保留尖锐细节与深度信息方面能力受限。

##### 4.2 Model-Optimisation-Based Offline Neural Methods

IOB-NST最大的缺陷就是效率问题。

MOB-NST有单模型对应单风格、单模型对应多风格、单模型对应任意风格。

###### 4.2.1 Per-Style-Per-Model Neural Methods

预先训练一个前馈（特定风格）网络，并在测试阶段通过单个前向传递产生一个程式化结果

###### 4.2.2 Multiple-Style-Per-Model Neural Methods

许多画（例如印象派）共享相同的绘画笔刷，只是在色彩上有所不同。提升PSPM的灵活性。