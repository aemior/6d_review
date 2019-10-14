# summay on 6D workshop 2018 

## 仍然存在的问题

- 遮挡Oclusion，和复杂Clutter场景
- 推广到多个物体Multiple Object
- 快速可靠物体建模
- 工业级别的运行速度
- 铰接式物体和形变物体

## From 3D Descriptors to Monocular 6D Pose: What Have We Learned

### 深度学习带来的影响

两个研究方向：

1. 物体的3D表示3d descriptors
2. RGB图像上的6D物体姿态估计

#### 学习3D特征表示

原因：物体的点云表示和3d mesh 不适合进行卷积

能直接使用点云和3D mesh的：

- 3dmatch:Learning local geometric descriptors from rgb-d reconstructions.
- Learning compact geometric features
- Fully-convolutional point networks for large-scale point clouds.

学习3D特征的：

- Pointnet: Deep learning on point sets for 3d classification and segmentation.
- Dynamic graph cnn for learning on point clouds.

#### RGB图像6D姿态估计

原因：人能通过RGB图像粗略估计物体6D姿态

通过3D BBOX的八个角来预测的：

- BB8: A scalable, accurate, robust to partial occlusion method for predicting the 3D poses of challenging objects without using depth.
- Real-time seamless single shot 6d object pose prediction.

通过regression

- Posecnn: A convolutional neural network for 6d object pose estimation in cluttered scenes.
- Deep-6dpose: Recovering 6d object pose from a single rgb image.

通过viewpoint

- SSD-6D: Making RGB-based 3D detection and 6D pose estimation great again.

通过patch pair？

- Deep model-based 6d pose refinement in rgb.

自动驾驶相关的

- Monocular 3dobject detection for autonomous driving
- 3d object proposals for accurate object class detection

#### 深度学习的潜力和限制

限制: 泛华能力和有限的及计算资源

潜力:最重要的是能够再RGB图像上估计物体6D姿态，虽然可能不如3D相机要好，可以与SLAM结合（家具姿态估计）做场景重建和场景理解

## Towards Robust 6D Pose Estimation: Physics-based Reasoning and Data Efficiency

仓库环境有大量的遮挡，实现在仓库环境中的姿态估计是进一步推广到一般场景的前提

目标需求：

- 3D场景理解
- 尽量减少对大量人工标记数据的依赖

实现上面的需求需要有4个方面的工作：

#### 使用带有物理引擎的数据合成流程

将环境的物理约束引入数据，是的数据的分布跟加真实，并且可以将一些特征参数化，如光照

#### 将分割中的像素级别概率分布运用于姿态估计中

原因：

亚马逊抓取挑战赛的常规套路：seg + alignment分割加配准

- Multi-view self-supervised deep learning for 6d pose estimation in the amazon pickin
   challenge
- Team delft’s robot winner of the amazon picking challenge 2016
- Super4PCS Fast Global Pointcloud Regis-tration via Smart Indexing
- Method for Registration of 3D Shapes(1992年文献ICP算法出处)

以上方法太依赖学习的模型，没有融合更多的信息？

使用概率约束姿态的搜索空间，加快速度**point pair features**？

- Robust 6D Object Pose Estimation with Stochastic Congruent Sets

#### 引入物理场景的一些特点来优化

首先获得一些不确定的姿态估计，然后使用物理信息，例如两个刚体不可能重叠，重力摩擦力等。来岁这些估计进行优化，得出最优估计。使得物体的姿态估计更加合理连贯。

- Improving 6d pose estimation of objects in clutter via physics-aware monte carlo tree search

蒙特卡洛搜索

#### 在线学习

自动标注线上运行的数据，例如使用robotic manipulator

- A self-supervised learning system for object detection using physics simulation and multi-view pose estimation.

首先用物理引擎训练一个基本的检测器，然后在一个合适的视角使用检测器检测物体姿态，然后移动到不同视角这时由于视角的信息是已知的，所以可以用视角变换信息加之前预测的姿态得到当前姿态并标注当前图像。标注的图像可以重新添加到训练集中训练检测器。

## Detecting Geometric Primitives in 3D Data – Bertram Drost

最好的算法依然基于手工特征。每一对点都会对物体的姿态做一个投票，统计所有点的投票估计物体姿态。点对特征由于对称物体的影响会造成错误的估计。用一些基本几何基本原件为物体建模，以这些原件的姿态来表示物体。解决对称性问题，为姿态估计加速。Base Method：

- Model globally, match locally: Efficient and robust 3D object recognition

## Parameteric Estimation of 3D Surfaces and Correspondences –Thibault Groueix

研究铰接的物体，自动将铰接物体分解并通过自动编码器学习物体的自由度表示。可以提高姿态估计的泛华能力，例如将一个椅子分解，学习各部分的姿态估计，最后可以估计任意姿态的椅子。也有望将物体姿态估计推广到非刚体上来。

- 3D-CODED: 3d correspondences by deep deformation（提出的编码器）
- Pointnet: Deep learning on point sets for 3d classification and segmentation.
- FAUST: Dataset and evaluation for 3d mesh registration（三维重建数据集）
- Atlasnet: A papier-mache approach to learning 3d surface generation.（分解物体）



## 其他关键点

- Introducing MVTec ITODD – a dataset for 3D object recognition in industry（halcon数据集）
- pose-error function 姿态误差公式，VSD

