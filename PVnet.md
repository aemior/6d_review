# PVnet 

最大亮点，**抗遮挡甚至能够识别出超出图像外的坐标点**。

## 综述和观点

### 对于6D姿态研究的梳理

第一代：模板匹配算法 手工特征，缺点是鲁棒性太差

第二代：端到端的深度学习方法，泛化性能太差(每种姿态都要学习，标签多，样本少，超出训练集的样本不好识别)

第三代：two-step深度学习方法，抗遮挡性能差

### 解决方法

- 接着第三代方法，对于抗遮挡能力，提出了一种 dense predictions，就是说只要物体有一部分可见，就可以对所有关键点给出完整的prediction。
- 对于关键点，使用投票策略+RANSAC得到一个概率分布的表示，可以进一步用于PNP算法当中，提高姿态估计的准确性

### related work

- 整体方法Holistic methods：模板匹配，鲁棒性太差。其他端到端的方法，如果使用神经网络模型做回归任务（回归出物体姿态），那么搜索空间太大，如果使用网络做分类任务（实现将姿态离散化）结果将会非常粗糙
- 关键点方法：SIFT之类的模板匹配，依赖于纹理特征。之前深度学习的关键点检测方法，使用Feature Map，如果keypoint超出图像将无法完成检测，抗遮挡能力差(遮掉关键点，或关键的featuremap）。
- keypoint属于稀疏sparse方法，稠密dense方法具有很好的抗遮挡能力，但是之前的dense方法直接投票姿态不太好应用。

思路就是用dense方法去投票keypoint，将两者的优势结合起来，实现抗遮挡截断truncation。

## 关键点

- 什么是RANSAC算法 
- 什么是PnP算法

## 文献

- Uncertainty-driven 6d pose estimation of objects and scenes from a single rgb image
- Posecnn:A convolutional neural network for 6d object pose estimation in cluttered scenes

多instances：

- Personlab: Person pose estimation and instance segmentation with a bottom-up, part-based,geometric embedding model

网络骨架

- Deep Residual Learning for Image Recognition

