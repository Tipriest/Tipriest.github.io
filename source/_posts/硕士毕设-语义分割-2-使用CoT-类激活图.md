---
title: 硕士毕设-语义分割-2-使用CoT_类激活图
date: 2025-03-28 19:20:46
tags: 硕士毕设 弱监督语义分割
---
## 零: 项目说明
是这样的一个事情，经过与deepseek的一番讨论和交流，DeepSeek为我设计了一个30天高强度学习计划，重点聚焦弱监督/无监督语义分割在野外场景的应用，结合理论与实践，并最终导向可落地的开源项目。目前开始了计划学习的第一天：

主要想法是做基于大模型的弱监督语义分割，主要步骤如下：

- **自动生成标注**：大模型生成`图像级标签`，`边框`，`涂鸦`、`点标注`等几种弱监督标注类型的标注：
- **生成伪标签**：采用基于`类激活图(CAM, Class Activation Mapping)`的方法 + `CRF后处理`，利用分类网络生成伪标签pseudo
- **训练分割模型**：将伪标签作为监督信号，训练`U-Net`、`DeepLabv3+`等分割网络。

代码在这里：不断改进中：
<https://github.com/Tipriest/30Days-for-segmentation>

---
### 参考引用
<https://zhuanlan.zhihu.com/p/29567314>
## 一: 采用思维链Cot优化大模型的image label效果
在上一篇文章中，主要采用了直接通过`CLIP模型+阈值判断`的方法和通过`VLM模型:llava`两种方法来对地形数据集的图片进行`Image Level标注`，如下图所示：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/44fe8cec56b94ce5b52ed7a4b0b96b69.png#pic_center)

主要发现了如下问题：

- CLIP模型对于主体的要求很高，当一个场景具有多个主体的时候，CLIP模型会拉低每一个图片中存在的物体的分类判断阈值。
- 直接使用llava模型在召回率上甚至不如CLIP，对于很多特殊的地形场景如curd往往没有输出，导致某一些召回率可能很低。

- **尝试采用思维链的方式进行优化:**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/d2608d2cd6f048058ac168a17f0f7561.png#pic_center)
比如，对于下面的这张图片
![在这里插入图片描述](<https://i-blog.csdnimg.cn/direct/2dfc2d51dced49a79908cce4ded72041.png#pic_center> =800x)
使用如上述所示的思维链的方式进行输出的结果如下所示:

```bash
Processing 4/60: ./steps/1_preprocess/key_frames/000202.jpg
 The image shows a patch of land with various elements that could be described from the perspective of the terrain. 
 There is no concrete road or paved paths visible in this particular section of the image. Instead, there appears 
 to be bare soil with some patches of grass and weeds growing on it. The color of the soil is a mix of brown and 
 gray, indicating it might be compacted dirt or soil that has been disturbed or left untended for some time.

There are no visible curbs or other distinctive features that would suggest a paved path or road nearby. In terms 
of colors, the soil predominantly appears in shades of brown and gray, while the patches of grass and weeds have 
varying tones of green. The image does not show any water bodies, nor is there any indication of yellow or 
red paved paths that might be adjacent to this area.

The perspective of the photo seems to be taken from a low angle looking up at the terrain, which gives an 
impression of the ground being uneven and unkempt. The overall scene suggests an outdoor setting with 
natural elements rather than urban infrastructure.
{"annotations": ["soil", "curb"]}
```

翻一下，大致是这样的结果:

```bash
处理4/60：./steps/1_preprocess/key_frames/000202.jpg
图像显示了一块土地，带有各种元素，可以从地形的角度描述。 图像的这一特定部分中没有可见的混凝土路或铺路路径。
取而代之的是，似乎有一些裸露的土壤，其中有一些草和杂草在上面生长。土壤的颜色是棕色和灰色的混合物，表明它可能是压实的污垢或土壤，
这些污垢或土壤已被打扰或没有一段时间。在颜色方面，土壤主要出现在棕色和灰色的阴影中，而草和杂草的斑块则具有不同的绿色色调。
该图像没有显示任何水体，也没有任何迹象表明可能与该区域相邻的黄色或红色铺路路径。整个场景表明，室外设置具有自然要素，而不是城市基础设施。

{"annotations": ["soil", "curb"]}
```

目前使用思维链的方式确实有了一定召回率上的提示，相比于原来的直接用VLM输出结果，但是结果还不够好，这个后续再继续优化....

## 二: 关于类激活图
Michael Yuan佬在自己的知乎上发表过一个非常易懂的[关于类激活图的科普文章](https://zhuanlan.zhihu.com/p/29567314)，这里借用一下佬的两张动图来直观说明一下：
CAM 指的是经过加权的特征图集重叠而成的一个特征图。它可以显示模型做出分类决策的依据主要来自于特征图集中的哪些特征图。（可以结合下面这张Michael Yuan佬画的图来理解）
![在这里插入图片描述](<https://i-blog.csdnimg.cn/direct/ba90f26f922e4982bec18f930fde4220.png#pic_center> =800x)
由于采用迁移学习策略，因而整个模型在训练时，会发生变化的参数只有分类器的权重矩阵 ，因此对于同一张图片，卷积层输出的特征图集始终不变，但分类概率会随着权重矩阵的变化而不断改变。这也就是模型学习的过程，即：到底哪些特征图对于提高分类准确率最有帮助？

可以看到，模型做出分类决策的依据来源于权重矩阵。那么如何进行可视化呢？
 矩阵本身只是一堆大小不一的权值而已，并不直观。不过我们可以注意到，权重矩阵对图像的理解基于对特征向量的加权，而特征向量背后是一个个特征图，因此可以跳过特征向量，直接将这些特征图用权重矩阵加权，再重叠合成为一张特征图，就可以很直观的看到到底模型是通过看哪片区域来做出判断的。
我在我的野外环境的数据集上，根据图像级标注生成的类激活图的效果，可以看到类激活图关注的主要是一个点而非全部，类似的类激活图也比较难用在我的语义分割伪标签生成任务中，后面的`CRF过程`会很麻烦。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/2308e972d6014beea732fe5e25bf959c.jpeg)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/ec568ed8a67c4565b635b87d552d39c9.png)
目前我的类激活图的效果还并不好，理想的状态应该是如下的：

类别 |激活图示意图（文字描述） |合理性判断标准
-------- | -----|  -----
马路 | 左侧大面积均匀暖色区域，渐冷至右侧边缘 | 覆盖真实马路区域，无侵入其他类别
车道线 | 中间细长亮黄色线条，周围马路区域为冷色  |线状激活宽度与实际车道线匹配
马路牙子  |右侧边缘窄条状高激活，分隔马路与红砖地 | 严格沿垂直方向分布，无横向扩散
红砖地 | 右侧斑块状/网格状激活，与马路牙子激活相邻  |纹理匹配，不污染左侧马路区域

因为类激活图做的人还是很多的，所以一些可能的改进方案如下：

- 分辨率限制：传统CAM基于最后一个卷积层，可能导致激活图粗糙（如车道线变粗）。可尝试：
 
 	- Grad-CAM++：增强细粒度结构的激活灵敏度
 
 	- HiResCAM：保留更高空间分辨率

- 多类别混淆：若多个类别同时高激活（如红砖地与马路牙子重叠），可能需要：

 	- 逐类别生成CAM（每个类别单独可视化）
 
 	- 结合注意力机制可视化（如Transformer-based模型的Attention Rollout）

- 语义分割专用CAM：对于分割模型（如DeepLab、UNet），建议：

 	- 使用SegCAM或Pixel-wise CAM直接关联像素级预测
 
 	- 可视化中间层特征图（观察低级边缘/纹理特征）
