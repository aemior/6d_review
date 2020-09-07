# CosyPose: Consistent multi-view multi-object 6D pose estimation

## Abstract

使用多个View的图片估计物体的6D姿态和相机视角。主要分为三个步骤，首先每个View上做目标检测和位置姿态估计，估计的方法本文提出，然后不同视角之间的结果作match,最后全局refinement最小化重投影误差。能解决对称物体。

## Introduction

第一段介绍背景，6D在机械臂抓取用有很重要的应用

第二段梳理历史指出不足，现在最好的方法是深度学习类的，但是没有用到场景信息

第三段详述问题，使用场景信息Seem Simple，但是还有问题，突出本文研究的价值。具体的挑战:First,没有相机的姿态信息，不同的物体姿态难以通过同一个reference frame表示出来。Second,初期的姿态有gross errors和噪声。Third，single view 的单目图片会有深度上的ambiguities。

第四段本文提出的三步方法解决了以上挑战，render-and-compare，RANSAC，global refinement procedure，比SOTA方法pix2pose高了34个平均点。



