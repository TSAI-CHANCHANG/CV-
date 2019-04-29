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

一般不同的IOB-NST算法是共享同一个IOB-IR（图片重建）技术，但是对于视觉风格的建模千差万别。（第一步style modelling千差万别，重建差不多）

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

有两种做法：1. 将有限数量的参数与每个风格绑定；2. 仍然只使用类似于PSPM的单网络，但是将style与content同时作为输入。

**1) Tying only a small number of parameters to each style**

使用相同的卷积参数但是缩放与迁移IN（instance normalisation）层的参数就能很有效的对不同风格进行建模。缺点是当学习的风格数量上涨后model的大小也会变得更大。此外也并未解决NST算法在笔刷细节、语义、多样性上的缺陷

**2) Combining both style and content as inputs**

Li 设计了一个选择单元用于风格选择，这是一个N维的one-hot向量。选择单元中每个bit都代表特定的风格。该方法依旧在保留微笑结构的相干性上有局限性。深度信息依旧没解决。

###### 4.2.3 Arbitrary-Style-Per-Model Neural Methods

**1) Non-parametric ASPM with MRFs**

先抽取出内容和风格的activation（这部分会在预训练的VGG网络中计算出），然后他们在比对每个内容patch，寻找最接近的sytle patch，然后交换两者（Style Swap）。程序化结果可以通过使用IOB-IR或者MOB-IR重建结果激励map获得。

内容可以得到很好的保留，但是风格却不能很好的反应。

**2) Parametric ASPM with Summary Statistics**

#### 6 评估方法

有两种评测方法可用于NST领域，分为定性分析(qualitative evaluation)和定量分析(quantitative evaluation).定性分析取决于观测者的审美判定，定量分析则侧重于时间复杂度，loss variation(变异)

##### 6.1 实验准备

介绍了使用的数据集，介绍了几大准则：（1）使用Torch7的同平台；（2）针对不同的算法分配各自的内容和风格的权重；（3）使用作者默认的参数。

#### 7 NST现在的应用

社交媒体用于创作自己的图像 绘画辅助工具 在创作动画电影和电脑游戏时可以节约成本

#### 8 未来挑战

有些NPR(non-photorealistic rendering)里的关键问题依旧留存，作为未来NST研究的挑战。

下文先讨论NPR与NST共同存在的问题，然后探讨NST领域特有的问题。

##### 8.1 分析方法

美学分析是NPR和NST共有的关键问题。第一个大问题就是评价分数会因人而异。第二个大问题是没有标准的评测图片集（用来衡量NST算法）

本文作者使用了NPR的测试集，但是作者认为因为NST算法不像NPR算法那样需要对风格图片有明显的限制，所以NPR的测试集仍然不是好的NST测试集。

寻找一个表现广阔可能属性并附有对所采用原理的详细叙述与图像特征测量值以及作品限制的讨论的benchmark至关重要。

##### 8.2 可解释的NST

NST的过程很像黑盒，使其难于掌控。

###### 8.2.1 Representation disentangling（解離表示）

解离表示的目标是构建一种维度层的可解释表示，即一个或几个特定的维度发生变化只与一个变量的factor有关，对其他factor独立。

对于风格转移，如果有一种表示法能使得所有factor都独立（比如颜色，图像，笔画大小，笔画方向和笔画构成），那么研究者在stylisation的过程中将能够自由的控制。

为了达成这个目标，有监督与非监督两种方法。

监督解离法的基本思想就是利用注释数据来监督输入与属性之间的mapping。然而在NST的情况下，建模和捕捉变量的aforementioned factors相当复杂。比如，我们十分难收集到这么一个图片集：有着不同的笔画方向但是却有着一样的颜色分布、笔画大小和笔画构成。

非监督法则相反的不需要注释，但是该法通常会让解离表示变得不可控制与不可解释。我们无法控制被解码入每个特定维度的东西是什么。

因此为了获得NST中的解离表示，第一要务就是找到如何定义、建模并捕捉NST中复杂的变量的方法。

###### 8.2.2 Normalisation methods

第一个出现的NST的normalisation方法为instance normalisation。当batch normalisation的batch大小为1时等效于instance normalisation。

有研究者认为因为instance normalisation让网络能够丢弃一些内容图像中的冲突信息并因此让学习更简单，所以该法有更良好的表现。

另一种解释是instance normalisation执行了一种style normalisation：normalize 特征统计数据（比如均值与方差）。每个图片可以直接被normalised到目标风格。最终其余网络只需要关心内容损失就行，让其更易于学习。

进阶版：conditional instance normalisation，也就是在instance normalisation layers缩放和转移参数。使用不同的affine参数，参数统计数据可以被normalised到不同的值，与之相关的，每个样本也可以被normalised到不同的风格。

但是成功原因仍然不明。为何feature statistics能够代表风格，或者更进一步，feature statistics能否代表所有风格。这与风格表示的可解释性有关了。

###### 8.2.3 Adversarial examples

有些研究表明深度分类网络很容易就被对抗性实例愚弄。这些对抗性实例是通过给输入图像加上扰动而得到的。

仍然不清楚为何对输入图片加上扰动后，人类仍然能识别，但是对于生成式风格转移网络就会给出如此不同的结果。并且仍然有一些例子在加上扰动后仍然可以被风格转移到目标风格。

##### 8.3 Three-way Trade-off in Neural Style Transfer 

三大因素此消彼长：速度、灵活性、质量