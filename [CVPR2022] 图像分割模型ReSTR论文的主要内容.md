# [CVPR2022] 图像分割模型ReSTR论文的主要内容

> 论文全称：《ReSTR: Convolution-free Referring Image Segmentation Using Transformers》
> 论文链接：[论文链接](https://arxiv.org/abs/2203.16768)

### 摘要：
Referring image segmentation是一个先进的语义分割任务，它的target不是一个predefined的class，而是由自然语言描述。
![模型主干](https://img-blog.csdnimg.cn/0b26513a653144fe8db5d2e715c6a962.png#pic_center)

### 现有的方法：
采用CNNs和RNNs分别提取视觉和语义特征，这些特征通过应用于联结两种特征的卷积层，被集成到多模态特征图。（这种方法简称为concatenate-convolution operation）。在多模态之上，最近的方法还应用了注意力机制，以便于特征图高效地捕捉semantic entities之间的相互关系。最终的多模态特征被作为input输入到segmentation module中。

### 现有方法的限制：
- 在处理每种模态的semantic entities间的长期相互关系时有困难，而referring image segmentation需要捕捉这一相互关系来精确地指示目标区域，因为语言表达通常包含了不同entities之间的复杂联系。
- 在对两种模态间的复杂相互关系建模时存在问题。现有的方法通过concatenate-convolution operation聚集视觉和语义特征，这是一种混合和手动的特征融合方式，因此不能足够灵活和高效地处理各种各样的referring image segmentation场景。

### ReSTR的方法：
- 通过transformer encoders（vision encoder和language encoder）提取视觉和语义特征，将一组**无重叠图像小块**和**词嵌入**作为模型输入，然后在考虑不同模态的特征间的长期相互关系条件下，提取它们的特征。通过对两种模态使用transformer，我们利用了从特征提取开始就捕获全局上下文并统一两种模态的网络拓扑的优势。
- 一个self-attention encoder将视觉和语义特征聚集为各个图像小块的多模态特征。有了self-attention layers，这种多模态融合编码器允许两种模态的特征间的复杂和灵活的相互关系。此外，融合编码器还带了一个class seed嵌入作为另一个输入。class seed嵌入被融合编码器自适应地变换为一个用语言表达描述的目标实体的**分类器**。
- 最终，多模态融合编码器的输出（各小块的多模态特征和自适应分类器）被作为input输入到分割译码器中。译码器以一种有粗到精的方式计算最终的分割图，自适应分类器作为测试每个图片小块是否包含目标实体的部分的分类器，被首次应用于多模态特征中。通过一系列的上采样和线性层，patch-level预测被转换为一个pixel-level分割图。
![整体架构](https://img-blog.csdnimg.cn/850483c9686544ea8c4a1d1401eba728.png#pic_center)

### 方法的理论基础（待完善）：
- Visual and Linguistic Feature Extraction：
![Visual and Linguistic Feature Extraction](https://img-blog.csdnimg.cn/daacee46827c4818840fcfe7dcb5d556.png#pic_center)
- Vision encoder：
![Vision encoder](https://img-blog.csdnimg.cn/3ba8f32156f5482f9a0ebe46740b6e62.png#pic_center)
- Language encoder：
![Language encoder](https://img-blog.csdnimg.cn/cbaecdadbe464053b69446b9314306cc.png#pic_center)


### ReSTR的贡献：
- 第一个面向referring image segmentation的convolution-free体系。它捕捉了视觉和语言模态的长期相互关系并通过transformer统一了两种不同模式的网络拓扑结构。
- 为了对两种模态的精细理解进行编码，为referring image segmentation设计了带有能被变换为自适应分类的class seed嵌入的多模态融合编码器。
- ReSTR在四个公共基准测试中达到了最先进的水平，而且模型体系简洁。

### 总结：
- ReSTR是第一个为referring image segmentation而生的convolution-free模型，为了捕捉特征提取的全局上下文信息，对于视觉和语言模态它都采用了transformers。
- ReSTR包含了由transformer组成的多模态融合编码器，可以完善而灵活地为两种模态特征的联系编码。
- ReSTR有一个segmentation decoder，可以以从粗到精的方式将patch-level预测细化到pixel-level预测。
- **潜在限制**：随着patch size的减小，计算成本呈二次增长。由于使用visual transformer时，密集预测任务的性能严重依赖于patch size，因此在性能和计算成本之间引入了不希望的权衡。（为了减缓这一限制，integrating linear-complexity transformer架构将会是一个理想的研究方向，也是未来的工作。）