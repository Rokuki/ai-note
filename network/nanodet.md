## NanoDet

NanoDet 是一种 **FCOS** 式的单阶段 **anchor-free** 目标检测模型，它使用 **ATSS** 进行目标采样，使用 **Generalized Focal Loss** 损失函数执行分类和边框回归（box regression）。

## 模型架构

![img](img\v2-a2d7b13b8b6b9a8df711f3a5058673f6_720w.jpg)

## FCOS

FCOS(FCOS: Fully Convolutional One-Stage Object Detection) 一阶全卷积目标检测

**基于FCN的逐像素目标检测算法**，实现了**无锚点（anchor-free）**、**无提议（proposal free）**的解决方案，并且提出了**中心度（Center—ness）**的思想，同时在召回率等方面表现接近甚至超过目前很多先进主流的基于锚框目标检测算法。

- anchor box free
- proposal free

### 优势

1. FCOS与许多基于FCN的思想是统一的，因此可以更轻松的重复使用这些任务的思路。
2. 检测器实现了proposal free和anchor free，显著的减少了设计参数的数目。设计参数通常需要启发式调整，并且设计许多技巧。另外，通过消除锚框，新探测器完全避免了复杂的IOU计算以及训练期间锚框和真实边框之间的匹配，并将总训练内存占用空间减少了2倍左右。
3. FCOS可以作为二阶检测器的区域建议网络(RPN)，其性能明显优于基于锚点的RPN算法。
4. FCOS可以经过最小的修改便可扩展到其他的视觉任务，包括实例分割、关键点检测。

### 算法详细

FCOS首先使用Backone CNN(用于提取特征的主干架构CNN)，另*s*为feature map之前的总步伐。

**与anchor-based检测器的区别**

- anchor-based算法将输入图像上的位置作为锚框的中心店，并且对这些锚框进行回归。

- FCOS直接对feature map中每个位置对应原图的边框都进行回归，换句话说FCOS直接把每个位置都作为训练样本，这一点和FCN用于语义分割相同。

  > FCOS算法feature map中位置与原图对应的关系，如果feature map中位置为![[公式]](https://www.zhihu.com/equation?tex=%28x%2Cy%29) ,映射到输入图像的位置是 ![[公式]](https://www.zhihu.com/equation?tex=%28%5Clfloor+%5Cfrac%7Bs%7D%7B2%7D+%5Crfloor%2Bxs%2C%5Clfloor+%5Cfrac%7Bs%7D%7B2%7D+%5Crfloor%2Bys%29) 。

- 在训练过程中，anchor-based算法对样本的标记方法是，如果anchor对应的边框与真实边框(ground truth)交并比大于一定阈值，就设为正样本，并且把交并比最大的类别作为这个位置的类别。
- 在FCOS中，如果位置 ![[公式]](https://www.zhihu.com/equation?tex=%28x%2Cy%29) 落入**任何**真实边框，就认为它是一个正样本，它的类别标记为这个真实边框的类别。



