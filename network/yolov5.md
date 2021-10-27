## 为什么使用yolov5
yolov5有很多trick，比如
数据增强方面：
- 马赛克
- 自适应anchors计算
- 图像缩放
- 透视变化
- 混合图片
- 颜色空间变换
性能方面：
- 模型更小，速度更快，适合部署

## yolov5结构特点
- 基础模块CBL: Conv，BN，Leaky ReLU
- 残差模块ResUnit
![dd](https://github.com/Rokuki/ai-note/raw/main/network/img/cbl.png)
