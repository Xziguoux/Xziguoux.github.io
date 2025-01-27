---
author: Moxiaozi
title: NMS算法基础
categories: ["algo"]
date: 2025-01-26T15:04:05+08:00
description: "NMS"
tags: ["NMS"]
draft: false  # 设置为 false 才会发布
toc: true     # 是否显示目录
math: true    # 是否启用数学公式
---

NMS将分成以下几篇进行讨论：
- NMS算法基本描述
- NMS算法代码实现以及性能分析与优化
- NMS不同框架支持以及性能调研
- NMS不同加速平台支持以及性能
本节只讲述基本算法描述.
# 1 NMS算法背景
[NMS]为非极大值抑制，在目标检测任务中主要对检测结果进行处理，使得结果中一个对象尽可能得到一个检测结果，过程如图1所示：
![抑制效果](https://moxiaozi-bucket.oss-cn-shanghai.aliyuncs.com/pics/nms/nms_algo_eff.png)
可以看出通过NMS，可以抑制掉过多重复结果。
# 2 单类别NMS算法描述
首先针对单类别NMS进行描述，基本算法如下所示：
![nms算法描述](https://moxiaozi-bucket.oss-cn-shanghai.aliyuncs.com/pics/nms/nms_algo.png)
上述算法简单描述如下：
1. 对于Box的置信度进行排序，选取最大值
2. 获取当前做大值box位置，并加入输出集合，同时从已有box中删去当前最大box
3. 对于当前box，计算其与剩余box的iou指标，当iou大于设定阈值，则对其进行抑制
4. 重复上述过程，遍历所有box, 最后输出保留box

整个抑制过程如下图示意：
![抑制过程示意](https://moxiaozi-bucket.oss-cn-shanghai.aliyuncs.com/pics/nms/nms_flow.png)

其中 IoU计算指标为交并比，即两个box相交面积比上面积的并集，可以通过下图进行表示 [IoU]
![IoU计算示意图](https://moxiaozi-bucket.oss-cn-shanghai.aliyuncs.com/pics/nms/vis_iou.jpg)
其计算逻辑如下图所示：
![交集计算示意图](https://moxiaozi-bucket.oss-cn-shanghai.aliyuncs.com/pics/nms/vis_inter_area.png)

# 3 多类别NMS处理
上面讲述了单个类别的抑制过程，对于多个类别，则是通独立运行NMS，每次针对单个类型进行输出。
# 4 总结
本文中主要讲述NMS算法基本描述，以及其IoU计算过程, 后续将结合算法进行代码实现以及性能分析优化

[1] https://ayameyao.github.io/NMS/Chapter1/4singleboj.html
[2] https://machinelearningspace.com/intersection-over-union-iou-a-comprehensive-guide/

