## 深度可分离卷积
- 逐层卷积（depthwise convolution）
- 逐点卷积（1 x 1）（pointwise convolution）
相比常规卷积减少了三分之二的参数量


## 宽度因子 alpha（Width Mutiplier）
在每一层对网络的输入输出通道数进行缩减，输入通道数由 M 到 alpha*M，输出通道数由 N 到 alpha*N

通常alpha在(0, 1]之间，比较典型的值有 1, 0.75, 0.5 和 0.25。计算量和参数数量减少程度与未使用宽度因子之前提高了1/alpha**2倍。
