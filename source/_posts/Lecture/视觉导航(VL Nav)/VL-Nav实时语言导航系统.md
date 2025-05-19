---
title: VL-Nav实时语言导航系统
tags:
  - Lecture
  - Navigation
date: 2025-05-19
---
### 一. 题目及作者
VL-Nav: Real-time Vision-Language Navigation with Spatial Reasoning
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F05%2F19%2Ff5251b00afac0531a430e682e687ad5e-20250519194123913.png)
`PhysOrd`: 将能量守恒定律，欧拉拉格朗日公式融入到神经网络中对运动进行预测
`SuperPC`: 一个统一的扩散模型能够同时对点云进行扩散，上采样和上色
`VL-Nav`: 实时的视觉导航系统
### 二. 主要内容
#### 2.1 目录
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F05%2F19%2Fdf13c71514aa7b0b03b91d01d3f9d83c-20250519194341361.png)
#### 2.2 Motivation&Background
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F05%2F19%2F9ce56cc0116d0310bfe23f18906c1092-20250519194553526.png)
本篇文章主要是基于目标的导航
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F05%2F19%2F8a6b6e8658b0bda0d5c1774fed9802c9-20250519194705901.png)
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F05%2F19%2F89e7e2a4218e68c5d3abf94cf24e93de-20250519194800101.png)
- 作者认为端到端的方法的泛化性有待提高
- 作者认为模块化学习方法需要大量真机数据去训练`神经网络模块`，有点奇怪
- 使用大语言模型，难以在移动端平台进行部署，
#### 2.3 方法
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F05%2F19%2Fb5cf620ea90ca77750c88331d7d20851-20250519195107755.png)
本文提出的方法由三个模块组成，第一个是视觉语言模块：包括一个词汇检测部分和语义分割部分；第二个是地图模块，主要包含地形分析和frontier探索部分，第三个是空间推理模块，主要包含...
方法的优点：在四个环境进行训练
获得下一步探索的前沿点的机制，一个是现在的待探索的边界，第二个是从任务目标过来的前沿点：
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F05%2F19%2F02810d6bab41343cfda97cf8647da534-20250519195745157.png)
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F05%2F19%2Fcf9b1534eab68ba9924b518cf768afe2-20250519195851333.png)
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F05%2F19%2Fd1a30791ca4593ecf24e999b1688631a-20250519200140272.png)
作者将检测出来的每一个东西都变化为一个gaussian distribution，最后变成多个高斯分布的叠加
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F05%2F19%2Fc1a6888fe6e0948b33329295c3e47bfe-20250519200454023.png)
#### 2.4 实机测试设置

![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F05%2F19%2F7183325cf656addc0a2be89e42878134-20250519200922299.png)
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F05%2F19%2Fe3829b7c27e61f6c7c5425c4d8d88b32-20250519201132238.png)
#### 2.5 实验结果
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F05%2F19%2F8757d98d7dfda091187f0f35877da2b1-20250519201335997.png)
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F05%2F19%2F29c8f64b5656e0d53edeccb967ff17a7-20250519201509058.png)
#### 2.6 Limitations and Future Works
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F05%2F19%2F67cfc1e82cb9e0d57181d21f770db8b7-20250519201750913.png)
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F05%2F19%2F98cde648e1f99b0685727d6878914cdf-20250519201923273.png)
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F05%2F19%2F5315889fab77aaad9f60956212c0d110-20250519202045420.png)
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F05%2F19%2F359bbb6b12121e44b83e6aa09020b292-20250519202126750.png)
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F05%2F19%2F3c4e38f70622ca3be168000f44b31c69-20250519202219644.png)
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F05%2F19%2F4547f785966ce00d6ce3231abbf440df-20250519202238920.png)
### 三. 评价
相比于周老师的无人机的探索方法(FUEL)，这篇文章主要的创新点就是增加了一个关于NBV(Next Best View)的语义评分机制，也是用大模型实现的，或者是用YOLO-World这种开放词汇的检测模型实现的，能够达到30Hz应该也就是YOLO的30Hz
- 确实是一个语义探索领域有意思的创新点
- 一开始的图画的着实是很花哨，有点感觉故意让人看不懂了，其实没必要的，老老实实画二维的图就挺好的
- ![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F05%2F19%2F54c9fe38f46c530d5d1e66a78b990fa0-20250519202928126.png)

- 仿真环境实验，没有仿真环境实验
- 实物实验做得比较粗糙，这四个场景其实很难覆盖什么，而且最后出的图的探索指标的探索效率到底指的是什么呢?与什么方法做对比呢