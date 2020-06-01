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

# 6-PACK

## Abstract

能做Categroy-Level 的pose，基于interframe 的 keypoint Pose track，KeyPoint 是学习出来的。

需要注意的点是文章里提到了Pose Track的问题，为什么需要做Track？

## Introduction

之前的工作，POSE一般只是做到 instance-Level 的， Track只是做到2D的，NOCS做的是Category，但是需要Track by detection（是不是Track的必要性？）。本文的目的是要做 Categrory 的 Pose Track，原理是基于Key Point的，而且这个KeyPoint 是 Unsupervised 学到的。比起BaseLine NOCS的优势是，使用了帧间信息，然后说实验上NOCS的dense point对噪音和遮挡都很敏感；比起其它Tracker的优势就是做到了Pose的Track。

## RelatedWork & Problem Def

最早的是说SIFT时代那一批，Keypoint recognition using randomized trees；Point matching as a classifi-
cation problem for fast and robust object pose estimation 加上LineMod时代那一批 Real-time 3d model-based tracking using edge and keypoint features for robotic manipulation；The moped framework: Object recognition and pose estimation for manipulation；给出的劣势是遮挡，复杂场景和光照变化不鲁棒。

最近的6D姿态检测是把 DeepIM 分为二Rendering，和把Segmention Driven分为Silhouette，给出的劣势是需要Model，他这个分法是为什么？

自动驾驶领域Category-Level的数据集非常充分，研究也非常充分。说是最为相近的方法，关键点是通过人工标记的，但是他把Semantic keypoints Object也算进去是为什么？

最后说NOCS，就是说实验上对噪音敏感。

总结就是无监督的Keypoint方法，而且不是直接使用KeypointNet，说是KeypointNet，只有在Clean和目标在中心的时候才有用，所以本文的方法里面引用了锚点机制，算是一大改进。

问题定义里面说，assume the initial pose of object is given，~~但是怎么given没有说清楚~~DenseFusion，只是说同一类物体按照相似的姿态放在一个规范的帧正中间，然后还说Robust to errors of initial pose,怎么做到，~~还有文献里面提到的SE(3)和SO(3)是个什么概念~~？SE(3)旋转加平移，SO(3)旋转

最后给了三篇inspire work：

- 6-dof object pose from semantic keypoints
- `Discovery of latent 3d keypoints via end-to-end geometric reasoning
- Viewpoints and keypoints



## DenseFusion

输入是用RGB-D做的，Intro里面也是有很多对3D detection 的观点，提到了一个前序工作，PointFusion 需要看看，实际内容里面，主要做的是Fusion，就是把 RGB 和 pointcloud 融合到一个网络里面，然后也不用PNP，做成Seamless的。目前理解上是卡在怎么做Embedding提取特征。