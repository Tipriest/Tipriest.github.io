---
title: 多传感器融合SLAM方法研究
date: 2025-04-02
tags:
  - Lecture
  - SLAM
---

香港科技大学-袁博
院子康

![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F02%2F127a734e37d295a71c687399562c7b29-20250402193222202.png)![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F02%2F8b1b25d91b5e31fe0f3678c70f26a89c-20250402193431346.png)
ORB-SLAM这个工作三篇
DSO的这个工作含金量很足，从应用和工程价值都很足
VINs-Mono 沈邵杰 秦通
什么是LIVO，为什么叫LIVO?

CT-ICP
	把一个lidar的sweep同时用开始和结束状态来表示，解决啥鸡生蛋还是蛋生鸡的问题
LIVO主要是张富老师团队的作品
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F02%2Fabccc93ef62d005964fa29a6577cb242-20250402194050974.png)
FAST-LIO和之前的求解量是三倍以上的往下降，主要是卡尔曼滤波处的一个问题的....
激光雷达的sweep主要是100ms一次的sweep
IG-LIO已经能够以20ms完成一个sweep的处理

这个时候激光雷达固有的比较低的频率成为了瓶颈

![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F02%2Fdeca0e1e2619a40098f80e6af9ec3c78-20250402194456557.png)

![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F02%2Fa148ffb877fbe6ab2bbe0f7e7885c0cb-20250402194532235.png)


![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F02%2F77bf7b13899d7fc210e25ee15f0d223e-20250402194736709.png)
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F02%2F51fbb8fff1796c40fa08624ca81aba0d-20250402194857276.png)



前向传播是IMU的值来做积分做的，反向传播是雷达反向传播的修正

![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F02%2F883401609946c8b0dcbb83f2c0327995-20250402195103990.png)


![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F02%2Fad7a8c008de444698dc63a327a33616e-20250402195110307.png)
SDV-Norm，不能实时，后面的加速播放

![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F02%2F4ddacdeaa1aa3bdf7c08c2090cc84c00-20250402195153256.png)
张富老师实验室开源的一个数据集

![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F02%2F266b26b9a71401fb9c0e32dcd1a5c48a-20250402195250529.png)
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F02%2Fc4e62c8160612dadf88506424b7ddfbf-20250402195455968.png)
