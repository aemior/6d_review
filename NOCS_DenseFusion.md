# NOCS

流程是先做分割然后，预测NOCS的坐标，然后结合深度图预测物体的姿态。提供了NOCS的数据集。

这个NOCS除了6D姿态以外还有一个SIZE Estimate

Introduction 和 survey 做得比较好，充分说明了Categrory 的 重要性， 融合了3d object detection,但是实际上并不怎么新，NOCS 相当于pixelwise 预测 8个角点，不过他后期不用PNP，然后怎么能做到categrory也需要再看一看。

## Introduction

1. 先把之前的6D方法都总结为Instance-level的，包括:Brachmann的Uncertain-driven 6d；LHCF；make 6d great again;BB8;Real Time Seamless;poseCNN
2. 把之前的一些自动驾驶的3D detction，总结为Category-level的。
3. Instance-level 需要模型，Category-level没有精确pose，所以提出了category-level 6D pose检测的概念
4. category姿态表示有问题，所以提出了NOCS，数据集短缺所以提供了数据集
5. 总结的贡献有三点
   - NOCS表示
   - 一个可以预测label，instance mask,NOCS的CNN网络，使用NOCS配合深度图预测物体姿态。
   - 数据集，使用混合现实的方法做的？

## Related Work

总结了Category 3D 目标检测，instance 6D姿态估计，category 4D 姿态估计，训练数据合成四个方面

#### Category-Level 3D Object Detection:

把目标定位和大小估计的方法总结为 3D Detection,特意提到了直接从点云数据中做检测的voxelnet。

#### Instance-Level 6 Dof Pose Estimation

列了一篇古老的文献，A method for registration of 3-d shapes

主要分为两类，第一类是直接匹配的，比如ICP或者手工特征点匹配，第二类是从目标的每个像素回归出POSE，比如人体姿态估计。NOCS接近于第二类，但是不需要CADmodel

#### Category-Leve 4 Dof Pose Estimation

假设了一个重力方向，所以是只有4个自由度，大多集中在室内物体上，沙发桌子汽车的尺度较大而且姿态比较单一，速度慢

#### Training Data Generation

只说之前有的方法合成出来的数据是飘在空中的，然后自己提出的混合现实的方法关注到上下文更加真实。

## 数据集

使用了一种平面检测的方法 Fast plane extraction in organized point clouds using agglomerative hierarchical
clustering 检测平面，然后现实场景作为背景，把CAD模型放进去。真实数据是使用了三维重建的方法，特制的方法，没有找到出处。

## 方法

NOCS 实际上是在MASK-RCNN后面装了3个head，分别是XYZ,XYZ是NOCS的坐标，最后会通过点云ICP去回归出实际的6D坐标，Least-squares estimation of transformation parameters between two point patterns。（为什么比SegICP好）

因为搜索空间太大， NOCS坐标预测上使用分类问题来代替回归问题。

处理对称性的方法是手工调整LOSS。

Baseline是用随机模型去，做SegICP，关键就是比这种Baseline好，相当于生成了一个Model不用随机选一个导致误差。

## DenseFusion

输入是用RGB-D做的，Intro里面也是有很多对3D detection 的观点，提到了一个前序工作，PointFusion 需要看看，实际内容里面，主要做的是Fusion，就是把 RGB 和 pointcloud 融合到一个网络里面，然后也不用PNP，做成Seamless的。目前理解上是卡在怎么做Embedding提取特征。