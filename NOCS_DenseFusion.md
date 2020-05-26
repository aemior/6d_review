## NOCS

Introduction 和 survey 做得比较好，充分说明了Categrory 的 重要性， 融合了3d object detection,但是实际上并不怎么新，NOCS 相当于pixelwise 预测 8个角点，不过他后期不用PNP，然后怎么能做到gategrory也需要再看一看。

## DenseFusion

输入是用RGB-D做的，Intro里面也是有很多对3D detection 的观点，提到了一个前序工作，PointFusion 需要看看，实际内容里面，主要做的是Fusion，就是把 RGB 和 pointcloud 融合到一个网络里面，然后也不用PNP，做成Seamless的。目前理解上是卡在怎么做Embedding提取特征。