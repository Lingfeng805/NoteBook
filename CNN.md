 PyTorch Tensor的通道排序：[batch,channel,height,width]

通过PIL,numpy导入的图片形式一般都是[height,width,channel]，若想将图片放入网络正向传播，需将其转换成Tensor的格式[channel,height,width]



# AlexNet

`AlexNet`是2012年`ISLVRC 2012`(ImageNet Large Scale Visual Recognition Challenge)竞赛的冠军网络，分类准确率由传统的70%+提升到80%+。

- `ISLVRC 2012`
  - 训练集：1,281,167张已标注图片
  - 验证集：50,000张已标注图片
  - 测试集：100,000张未标注图片

![AlexNet](E:/software/Typora/pictures/image-20220410184304888.png)

- 该网络的**亮点**：
  - 【1】首次利用GPU进行网络加速训练；
  - 【2】使用`ReLU`激活函数，而不是传统的`Sigmoid`激活函数以及`Tanh`激活函数；
  - 【3】使用了`LRN`局部响应归一化；
  - 【4】在全连接层的前两层中使用了`Dropout`随机失活神经元操作，以减少过拟合。
  - 【`Dropout`一般放在全连接层与全连接层之间】

`input`:224×224×3

- `Conv1`:
  - 卷积核个数：48×2=`96`（原论文作者用了2块GPU做并行处理，上图的上下网络结构一样）
  - 卷积核尺寸：`11×11`
  - 步长Stride:`4`
  - padding:`[1,2]`(特征矩阵左、上加上`1`列0，右/下加上`2`列0)
  - 卷积层计算公式$W_2=(W_1-F+2P)/S + 1$=[224-11+(1+2)/4+1]=`55`
  - `input`----(`Conv1`)---->`feature1`:`96个55×55`
- `Maxpool1`:
  - 池化核尺寸：`3×3`(上图画成了5)
  - padding：`0`
  - Stride:`2`
  - 池化层计算公式：$W_2 = (W_1-F)/S+1$=[(55-3)/2+1]=`27`
  - `feature1`:`96个55×55`----(`Maxpool1`)---->`feature2`:`96个27×27`
- `Conv2`:
  - 卷积核个数：128×2=`256`
  - 卷积核尺寸：`5×5`(上图画成了3)
  - 步长Stride:`1`
  - padding:`[2,2]`
  - 卷积层计算公式$W_2=(W_1-F+2P)/S + 1$=[27-5+(2+2)/1+1]=`27`
  - `feature2`:`96个27×27`----(`Conv2`)---->`feature3`:`96个27×27`
- `Maxpool2`:
  - 池化核尺寸：`3×3`
  - padding：`0`
  - Stride:`2`
  - 池化层计算公式：$W_2 = (W_1-F)/S+1$=[(27-3)/2+1]=`13`
  - `feature3`:`96个27×27`----(`Maxpool2`)---->`feature4`:`96个13×13`
- `Conv3`:
  - 卷积核个数：192×2=`384`
  - 卷积核尺寸：`3×3`
  - 步长Stride:`1`
  - padding:`[1,1]`
  - 卷积层计算公式$W_2=(W_1-F+2P)/S + 1$=[13-3+(1+1)/1+1]=`13`
  - `feature4`:`96个13×13`----(`Conv3`)---->`feature5`:`384个13×13`
- `Conv4`:
  - 卷积核个数：192×2=`384`
  - 卷积核尺寸：`3×3`
  - 步长Stride:`1`
  - padding:`[1,1]`
  - 卷积层计算公式$W_2=(W_1-F+2P)/S + 1$=[13-3+(1+1)/1+1]=`13`
  - `feature5`:`384个13×13`----(`Conv4`)---->`feature6`:`384个13×13`
- `Conv5`:
  - 卷积核个数：128×2=`256`
  - 卷积核尺寸：`3×3`
  - 步长Stride:`1`
  - padding:`[1,1]`
  - 卷积层计算公式$W_2=(W_1-F+2P)/S + 1$=[13-3+(1+1)/1+1]=`13`
  - `feature6`:`384个13×13`----(`Conv5`)---->`feature7`:`256个13×13`
- `Maxpool3`:
  - 池化核尺寸：`3×3`
  - padding：`0`
  - Stride:`2`
  - 池化层计算公式：$W_2 = (W_1-F)/S+1$=[(13-3)/2+1]=`6`
  - `feature7`:`256个13×13`----(`Maxpool3`)---->`feature8`:`256个6×6`
- `FC1`:
  - `feature8`:`256个6×6`----(flatten)---->`9216`----(`FC1`)---->`2048`
- `FC2`:
  - `2048`----(`FC2`)---->`2048`
- `FC3`:
  - `2048`----(`FC3`)---->`1000`(个类别)

| `layer name` | `kernel size` | `kernel num` | `padding` | `stride` |
| ------------ | ------------- | ------------ | --------- | -------- |
| `Conv1`      | 11            | 96           | [1,2]     | 4        |
| `Maxpool1`   | 3             | None         | 0         | 2        |
| `Conv2`      | 5             | 256          | [2,2]     | 1        |
| `Maxpool2`   | 3             | None         | 0         | 2        |
| `Conv3`      | 3             | 384          | [1,1]     | 1        |
| `Conv4`      | 3             | 384          | [1,1]     | 1        |
| `Conv5`      | 3             | 256          | [1,1]     | 1        |
| `Maxpool3`   | 3             | None         | 0         | 2        |
| `FC1`        | 2048          | None         | None      | None     |
| `FC2`        | 2048          | None         | None      | None     |
| `FC3`        | 1000          | None         | None      | None     |



# VGG

`VGG`在2014年由牛津大学研究组VGG(Visual Geometry Group)提出，斩获该年ImageNet竞赛Localization Task(定位任务)第一名和Classification Task第二名。

![一文读懂VGG网络](E:/software/Typora/pictures/v2-dfe4eaaa4450e2b58b38c5fe82f918c0_1440w.jpg)

`Conv`的步长:1，padding=1;

`Maxpool`的size=2，strde=2;

![image-20220410212211746](E:/software/Typora/pictures/image-20220410212211746.png)

【经常使用配置D】：13个Conv+3个FC

- 该网络的**亮点**：
  - 通过堆叠多个`3×3`的卷积核来**替代**大尺度卷积核（**减少所需参数**）
  - 可通过**堆叠两个`3×3`的卷积核替代`5×5`的卷积核，堆叠三个`3×3`的卷积核替代`7×7`的卷积核**（拥有相同的**感受野**）
- 感受野计算公式：$F(i)=(F(i+1)-1)×Stride+Ksize$
  - `F(i)`:第i层感受野；
  - `Stride`:第i层的步长；
  - `Ksize`:卷积核或池化核尺寸；



# GoogLeNet

`GoogLeNet`在2014年由Google团队提出，斩获当年ImageNet竞赛中Classification Task第一名。

- 该网络的**亮点**：

  - 引入`Inception`结构（融合不同尺度的特征信息）
  - 使用`1×1`的卷积核进行降维以及映射处理
  - 添加两个辅助分类器帮助训练
  - 丢弃全连接层，使用平均池化层（大大减少模型参数）

  ![preview](E:/software/Typora/pictures/v2-766c3f59d3791da39ad805606d6445f6_r.jpg)

- 为什么要提出`Inception`?
  - 一般来说，提升网络性能最直接的办法就是**增加网络深度和宽度**，但一味地增加，会带来诸多问题：
    - 参数太多，若训练集有限，很容易产生过拟合；
    - 网络越大、参数越多，计算复杂度越大，难以应用；
    - 网络越深，容易出现梯度弥散问题（梯度越往后越容易消失），难以优化模型；
  - 因此，希望在增加网络深度和宽度的同时，**减少参数**，自然便想到将全连接变成稀疏连接。但实现上，全连接变成稀疏连接后实际计算量并不会有质的提升，因为大部分硬件是针对密集矩阵计算优化的，稀疏矩阵虽然数据量少，但是计算所消耗的时间却很难减少。因此，Google提出`Inception`。

- `Inception`结构：

  - 把多个卷积或池化放在一起组成一个网络模块，设计神经网络时以模块为单位去组装整个网络结构。
  - 

  ![preview](E:/software/Typora/pictures/v2-effa75269cba3c8038c49185e9e8368a_r.jpg)

  - ![preview](E:/software/Typora/pictures/v2-39e361ad7cdbbb521f6be1bdb57452e0_r.jpg)
    - 【1】使用`1×1`卷积来进行**降维**；
    - 【2】在多个尺寸上同时进行卷积再聚合。

- 针对分类任务，GoogLeNet优于VGG；



# ResNet

`ResNet` 在2015 年由微软实验室提出，斩获当年ImageNet竞赛中分类任务第一名，目标检测任务第一名。获得COCO数据集中目标检测第一名，图像分割第一名。

- 该网络中的亮点：
  - 超深的网络结构（突破1000层）
  - 提出`residual`模块
  - 使用`Batch Normalization`加速训练（丢弃`dropout`）
- 残差网络的主要贡献是发现了**退化现象**(`Degradation`)，并针对退化现象发明了**快捷连接**(`Shortcut connection`)，极大的消除了深度过大的神经网络训练困难问题。
- 一般深层网络面临的问题：
  - **梯度消失**：假设每层的误差梯度是一个小于1的数，反向传播过程中，每向前传播一次都需要乘以该小于1的系数，随着网络越来越深，则所乘的小于1的系数越来越多，越趋近于0，导致梯度消失；
  - **梯度爆炸**：假设每层的误差梯度是一个大于1的数，反向传播过程中，每向前传播一次都需要乘以该大于1的系数，随着网络越来越深，则所乘的大于1的系数越来越多，使得梯度越来越大，导致梯度爆炸。
- `residual`结构：
  - 分为2种，一种有**bottleneck**结构，即下图右中的`1×1`卷积层，用于**先降维再升维**，出于**降低计算复杂度的考虑**；另一种没有**bottleneck**结构，如下图左，称之为**basic block**。
  - ![preview](E:/software/Typora/pictures/v2-fc3f5edf326a50b62f2bdacad24f035b_r.jpg)

![https://arxiv.org/abs/1512.03385](E:/software/Typora/pictures/3u8Wwj.png)

![ResNet](E:/software/Typora/pictures/image-20220415145313125.png)

## ResNeXt

`ResNeXt`是`ResNet`和`Inception`的结合体，不同于`Inception v4`的是，`ResNeXt`不需要人工设计复杂的`Inception`结构细节，而是每个分支都采用相同的拓扑结构。`ResNeXt`的本质是**分组卷积**，通过变量基数来控制组的数量。**组卷积**是普通卷积核深度可分离卷积的一个折中方案，即每个分支产生的`feature Map`的通道数为n。

- `Inception`的模式：`split-transform-merge`，即先将输入分配到多路，然后每一路进行转换，最后再把所有支路的结果融合。
  - `split`:将数据x分成D个特征；
  - `transform`: 每个特征经过一个线性变换；
  - `merge`:通过单位加合成最后的输出。
- `ResNet`基本结构（下图左），`ResNeXt`基本结构（下图右）

![preview](E:/software/Typora/pictures/v2-ee942d228efdaaff277c8a9a8b96a131_r.jpg)

- `split-transform-merge`是通用的神经网络的**标准范式**，

  ![preview](E:/software/Typora/pictures/v2-324282edcbfbd476fbe406c5fddb8dfd_r-16497511004317.jpg)

图(a)是`ResNeXt`基本单元，若把输出那里的`1×1`卷积合并到一起，得到等价的网络(b)拥有和`Inception-ResNet`相似的结构，而进一步把输入的`1×1`卷积合并到一起，得到等价网络(c)则和**通道分组卷积**的网络有相似的结构。

![preview](E:/software/Typora/pictures/v2-623f58e8e2e93bbd19e4f33f5f71423d_r.jpg)



# MobileNet

传统卷积神经网络，**内存需求大**、**运算量大**，导致无法在移动设备以及嵌入式设备上运行。

`MobileNet`由Google团队在2017年提出，专注于移动端或者嵌入式设备中**轻量级**CNN网络。相比传统卷积神经网络，在准确率小幅降低的前提下，大大减少模型参数与运算量。（相比VGG16准确率减少0.9%，但模型参数只有VGG的1/32）

- 该网络的亮点：
  - `Depthwise separable convolution`
    - `depthwise convolution`（大大减少运算量和参数量）
    - `pointwise convolution`;
  - 增加超参数：$α$和$β$；（$α$:控制卷积层卷积核个数; $β$:控制输入图像大小）

- **卷积操作**：

  - 传统卷积：

    - 卷积核`channel`=输入特征矩阵`channel`;
    - 输出特征矩阵`channel`=卷积核个数；

    ![传统卷积](E:/software/Typora/pictures/image-20220413221756370.png)

  - **DW卷积**：

    - 卷积核`channel`=1;
    - 输入特征矩阵`channel`=卷积核个数=输出特征`channel`；

    ![DW卷积](E:/software/Typora/pictures/image-20220413222313822.png)

  - **PW卷积**：pointwise conv

    - 卷积核大小为`1`的普通卷积；

    ![PW卷积](E:/software/Typora/pictures/image-20220413222720748.png)

- `Depthwise Separable Conv`深度可分卷积：`DW`+`PW`

  - 参数量比较：

    - 普通卷积：

      ![普通卷积参数量](E:/software/Typora/pictures/image-20220413223019887.png)

      $D_k*D_k*M*D_F*D_F*N$（1）

    - **DW+PW**:

      ![DW+PW](E:/software/Typora/pictures/image-20220413223338605.png)

      $D_k*D_k*M*D_F*D_F+D_F*D_F*M*N$（2）

    - $(1)/(2)=N+D_k^2$:理论上普通卷积计算量是DW+PW的8~9倍。

- `MobileNet V1`网络在使用过程中，`DW`卷积在训练完之后，它的部分卷积核会废掉，DW卷积核的参数大部分变成了0；

- `Depthwise Separable Convolution`是`MobileNet`的基本组件。但在真正应用中会加入`batchnorm`，并使用ReLU激活函数，所以其基本结构如下图所示：
  - ![img](E:/software/Typora/pictures/v2-2fb755fbd24722bcb35f2d0d291cee22_1440w.jpg)
- `MobileNet`网络结构：
  - ![image-20220414114331267](E:/software/Typora/pictures/image-20220414114331267.png)
  - 首先是一个`3×3`卷积，然后后面堆叠`Depthwise Separable Convolution`（`DW+PW`），可见其中部分`DW`会通过`Stride=2`进行下采样。然后采用`average pool`将`feature`变成`1×1`，根据预测类别加上全连接层，最后是一个`softmax`层。
- `MobileNet`网络的计算与参数分布：
  - ![img](E:/software/Typora/pictures/v2-512f1388c2be4153924f6c9817ac5e5a_1440w.jpg)
  - 整个网络计算量基本集中在`1×1`卷积上，卷积底层实现一般通过一种im2col方式实现，其需要内存重组，但是当卷积核为1x1时，其实就不需要这种操作了，底层可以有更快的实现。
- `MobileNet`网络的效果：
  - ![img](E:/software/Typora/pictures/v2-6d828ae295daa68098ff08cc5982b91a_1440w.jpg)

## MobileNet V2

`MobileNet V2`网络由Google团队在2018年提出，相比`MobileNet V1`网络准确率更高，模型更小。该网络依然使用了`v1`中的`Depthwise Separable Convolution`，区别在于`v2`中又引入了**残差结构**和**Bottleneck**层。

- 该网络的亮点：
  - `Inverted Residuals`(倒残差结构)
  - `Linear Bottlenecks`
- `v1`中的`Depthwise Separable Convolution`结构将**空间相关性**和**通道相关性**分离；
  - <img src="E:/software/Typora/pictures/v2-eebfc46483a20969cebf81543c31a70c_r.jpg" alt="DW+PW" style="zoom:50%;" />
- `Linear Bottlenecks`：
- `Inverted Residuals`：
  - `Residual block`(残差块-带瓶颈结构):
    - 【1】`1×1`卷积降维；
    - 【2】`3×3`卷积；
    - 【3】`1×1`卷积升维；
  - `Inverted Residual Block`：
    - 【1】`1×1`卷积升维；
    - 【2】`3×3`卷积；
    - 【3】`1×1`卷积降维；
  - ![倒残差结构](E:/software/Typora/pictures/image-20220414150112342.png)
- `Bottleneck residual block`结构：
  - ![image-20220414150449308](E:/software/Typora/pictures/image-20220414150449308.png)
  - <img src="E:/software/Typora/pictures/v2-75e2e8b9027036d8388c4f8a1e67a1d7_r.jpg" alt="倒残差" style="zoom:45%;" />
- `MobileNet-v2 `网络结构：
  - ![v2结构](E:/software/Typora/pictures/image-20220414151154741.png)
    - `t`:扩展因子；`c`:输出特征矩阵深度channel；`n`:bottleneck的重复次数；`s`：步距，针对第一层，其他层为1。
- `MobileNet-v2 `网络分类效果：
  - ![效果](E:/software/Typora/pictures/image-20220414151345656.png)

 ## MobileNet V3

- 更新内容：
  - 更新Block(bneck);
    - 加入SE模块；
    - 更新激活函数；
      - 激活函数使用`h-swish`而不是`ReLU6`:
        - v1和v2都使用`ReLU6`，v3使用`h-swish`：
        - 并非整个模型都使用了h-swish，模型的前一半层使用常规ReLU（**第一个conv层之后的除外**）；作者发现，h-swish仅在更深层次上有用。 此外，考虑到特征图在较浅的层中往往更大，因此计算其激活成本更高，所以作者选择在这些层上简单地使用ReLU（**而非ReLU6**），因为它比h-swish省时。
  - 使用`NAS`搜索参数(Neural Architecture Search);
  - 重新设计耗时层结构。
    - **减少第一个卷积层的卷积核个数(32-->16)**；v1和v2都从具有32个滤波器的常规`3×3`卷积层开始，而实验表明这是一个相对耗时的层，只要16个滤波器就足够完成对`224×224`特征图的滤波。
    - 精简`Last Stage`;
- `Squeeze and Excitation (SE)`:`SE block`并不是一个完整的网络结构，而是一个子结构，可以嵌入到其他分类或检测模型中。作者在文中将 SENet block 和 ResNeXt 插入到现有的多种分类网络中，都取得了不错的效果。**SENet 的核心思想在于通过网络根据 loss 去学习特征权重，使得有效的 feature map 权重大，无效或效果小的 feature map 权重小的方式训练模型达到更好的结果**。当然，SE block 嵌入在原有的一些分类网络中不可避免的增加了一些参数和计算量，但是在效果面前还是可接受的。最方便的是SENet思路简单，很容易扩展在已有的网络结构中。
  - ![img](E:/software/Typora/pictures/1226410-20201216160010512-428302047.png)
  - 通过对**卷积**得到的`feature map`进行处理，得到一个和通道数一样的**一维向量**作为每个通道的**评价分数**，然后将该分数分别施加到对应的通道上，得到其结果，就是在原有的基础上只添加一个模块。
  - <img src="E:/software/Typora/pictures/v2-c6fe52122f9ad28d8e5322bc62f4eb2e_1440w.jpg" alt="SE" style="zoom:90%;" />
  - [代码实现方式](https://www.ebaina.com/articles/140000012469)

# ShuffleNet v1

如何利用有限的计算资源来达到最好的模型精度。



- `ShuffleNet`核心：主要思想即在**分组卷积**后加一个`channel shuffle`
  - `Pointwise group convolution`
  - `channel shuffle`



## ShuffleNet v2

`FLOPS`:**每秒浮点计算次数**，可以理解为计算速度。是衡量硬件性能的一个指标。

`FLOPs`:**浮点运算数**，理解为计算量。是衡量算法/模型的复杂度。





# EfficientNet





## EfficientNet v2



# Vision Transformer

## Self-Attention(2017年 Google提出)

[Attenion Is All You Need](https://arxiv.org/abs/1706.03762)

[推荐博文](https://blog.csdn.net/qq_37541097/article/details/117691873)

  ## Vision Transformer

出自[2020CVPR](https://arxiv.org/abs/2010.11929)

[推荐博文](https://blog.csdn.net/qq_37541097/article/details/118242600)

# Swin-Transformer



# ConvNeXt

**让卷积模型借鉴`Transformer`架构中的各种方法，但始终不引入注意力模块**。

虽然`Transformer`在视觉上大获成功，但**全局注意力机制**的复杂度是与输入图像尺寸的平方呈正比的。对ImageNet图像分类任务的`224×224`、`384×384`分辨率来说还算可以接受，需要高分辨率图像的实际应用场景下就不太理想。

`Swin Transformer`靠重新引入CNN中的**滑动窗口**等诸多特性弥补上述问题，但也让`Transformer`变得更像CNN了。

<img src="E:/software/Typora/pictures/image-20220415115740945.png" alt="image-20220415115740945" style="zoom:80%;" />

- 该网络改动之处：
  - 【0】训练方法；
  - 【1】宏观设计；
  - 【2】引入`ResNeXt`;
  - 【3】反转瓶颈层；
  - 【4】增大卷积核；
  - 【5】微观设计；
- 【0】训练方法
  - **epoch**:从90增加到300；
  - 优化器：使用`AdamW`优化器；
  - **数据增强**：引入了Mixup、Cutmix、RandAugment和Random Erasing；
  - **正则化**：使用随机深度（`Stochastic Depth`）和标签平滑（`Label Smoothing`）；
- 【1】宏观设计
  - `block`数量的比例调整：原版`ResNet-50`的4个阶段按(3,4,6,3)分配，改为`1:1:3:1`的(3,3,9,3,)分配；
  - `stem`层：原版`ResNet-50`为`s=2`的`7×7`卷积层+`3×3`最大池化层，即相当于对输入图像做了4倍下采样；改为`s=4`的`4×4`非重叠卷积；
- 【2】引入`ResNeXt`
  - `ResNeXt`的核心思想是**分组卷积**，同时为了弥补模型容量上的损失**增加了网络宽度**。将分组数与输入通道数相等，设为96。这样每个卷积核处理一个通道，只在空间维度上做信息混合，获得与自注意力机制类似的效果。
- 【3】反转瓶颈层
  - 采用反转瓶颈层后，虽然depthwise卷积层的FLOPs增加了，但下采样残差块作用下，整个网络的FLOPs反而减少。
- 【4】增大卷积核
  - 在反转瓶颈层的基础上把depthwise卷积层提前，卷积核采用`7×7`。
- 【5】微观设计
  - 激活函数：卷积模型主要使用的是简单高效的ReLU，GELU比ReLU更平滑，被BERT、GPT-3等NLP模型以及ViT采用。使用`GELU`。
    - `Transformer`块中仅MLP块中存在激活函数，而CNN普遍做法是每个卷积层后都加一个激活函数。ConvNeXt尝试只保留了两个1x1层之间的GELU激活函数，与Transformer做法保持一致
    - 归一化层的数量同样做了减少。
  - 归一化层：用`LN`层归一化替换`BN`批次归一化。
  - 分离下采样层：`ResNet`下采样有残差块执行，`Swin Transformer`使用单独的下采样层，`ConvNeXt`使用步长为2的`2×2`卷积执行下采样，结果却造成了训练不稳定。好在后来找到解决办法，在每个下采样层前面、stem前面和最后的全局平均池化前面都加上LN。

![img](E:/software/Typora/pictures/d511a5e6d71241fdb6394d6c5a358105.png)
