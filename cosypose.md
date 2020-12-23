# CosyPose: Consistent multi-view multi-object 6D pose estimation

## Abstract

使用多个View的图片估计物体的6D姿态和相机视角。主要分为三个步骤，首先每个View上做目标检测和位置姿态估计，估计的方法本文提出，然后不同视角之间的结果作match,最后全局refinement最小化重投影误差。能解决对称物体。

## Introduction

第一段介绍背景，6D在机械臂抓取用有很重要的应用

第二段梳理历史指出不足，现在最好的方法是深度学习类的，但是没有用到场景信息

第三段详述问题，使用场景信息Seem Simple，但是还有问题，突出本文研究的价值。具体的挑战:First,没有相机的姿态信息，不同的物体姿态难以通过同一个reference frame表示出来。Second,初期的姿态有gross errors和噪声。Third，single view 的单目图片会有深度上的ambiguities。

第四段本文提出的三步方法解决了以上挑战，render-and-compare，RANSAC，global refinement procedure，比SOTA方法pix2pose高了34个平均点。

Fig中说明，本方法能accurately reconstructs the scene

## Method

### 单目的估计方法

基于DeepIM（2018ECCV）：一个迭代预测位姿的方法，迭代的初始位姿可由其他方法给出，也可以定义一个canonical pose（根据bounding box 设一个），迭代过程需要获得相机内参。

用于描述旋转参数的方法：使用两个向量描述，证明用于神经网络时比四元数的描述要好。On the Continuity of Rotation Representations in Neural Network

改进点

1. 骨架用了EfficientNet-B3
2. 旋转表示用了新的方法，更适合神经网络训练
3. 提出了一个新的损失，可以应对对称性（用渲染图判断对称性;计算点云中每个点的距离误差，考虑对称性，求平均）
4. 使用了真实焦距，根据cropped images？
5. 使用合成数据预训练，再在合成数据和真实数据上fine-tuned
6. RGB数据增强，在T-LESS上提高了很多的百分点

## Results

## Metrics

**ADD** 3D模型顶点的平均距离误差，**ADD-S** 对称的ADD度量方法

**VSD** On Evaluation of 6D Object Pose Estimation 可以消除由于遮挡造成的ambiguity：GT和PD的pose根据模型渲染出两个深度图来，由场景的深度图剔除上述两个深度图遮挡的部分（当场景深度图小于渲染，认为遮挡），剔除遮挡的两个深度图mask求并集，并集内的点算深度图误差（有个归一化操作），求所有点平均。