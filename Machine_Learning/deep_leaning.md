# 深度学习

## 常见架构
- CNN（以下均为局部感受野（卷积）作为核心特征提取手段，且均为浅层到深层）
  - ResNet
  - Yolo
  -（V-Net）
- Transformer
  - 基于自注意力（Self-Attention）机制
  - BERT
  - GPT
  - ViT
- RNN
- MLP  

## CNN
核心：卷积、池化  
擅长做图像、网格数据处理  
复杂度：O(n)局部感受野  
部分并行（依赖卷积核大小）	

可以把整张图像的特征想象成特征池，突出的特征会高一些。特征的由来是对图像的每一块像素区域进行卷积核大小的卷积，计算完后通过设置的步长计算下一个。对卷积得到的特征使用激活函数进行激活，可以解决非线性问题。（由于卷积操作是由输入矩阵与卷积核矩阵进行相差的线性变化关系，需要激活层对其进行非线性的映射。）对特征池中的所有特征进行池化，缩小池中的信息，保留最显著的特征。池子可能不止一个，有浅层和深层，浅层可能为边缘，深层可能为物体检测。所以浅层是对图像进行初步的边缘、颜色、纹理，深层是学习如何组合浅层输出的特征，如语义。  
**实际代码中，这些“池子”就是多维张量（如[batch, channels, height, width]）**

### Yolo 目标检测 2D
Backbone (CNN) → Neck (FPN/PAN) → Head (分类+回归)  


### ResNet 分类/特征提取 2D
分组卷积+残差  
x → [Conv1x1, Conv3x3(groups=32), Conv1x1] + x → ReLU  


### V-Net 医学分割 3D
U-Net 2D: [Downsample → 2D Conv] ×4 → [Upsample + Skip] ×4  
V-Net 3D: [Downsample → 3D Conv] ×4 → [Upsample + 3D Skip] ×4  


### CNN架构对比表（YOLO vs ResNet vs V-Net）

| 对比维度         | YOLO (以YOLOv8为例)                          | ResNet (以ResNet50为例)                  | V-Net                              |
|------------------|---------------------------------------------|-----------------------------------------|-----------------------------------|
| **核心结构**     | CSPDarknet + PANet + 检测头                 | 残差块 (Bottleneck) + 全局平均池化       | 3D U-Net结构 + 跳跃连接            |
| **输入维度**     | 2D RGB图像 (e.g., 640×640×3)                | 2D RGB图像 (e.g., 224×224×3)            | 3D体数据 (e.g., 128×128×128×1)    |
| **主要任务**     | 实时目标检测 (分类+定位)                    | 图像分类                                | 医学影像分割                      |
| **特征提取差异** | 多尺度特征融合 (FPN/PANet)                  | 深层语义特征 (通过残差连接保留梯度)       | 3D空间上下文特征 (体素级分析)      |
| **核心创新点**   | 1. 单阶段检测<br>2. Anchor-Free设计         | 1. 残差学习<br>2. 恒等映射              | 1. 3D卷积<br>2. Dice Loss         |
| **输出形式**     | 边界框 (x,y,w,h) + 类别概率                 | 类别概率向量                            | 3D分割掩码 (与输入同尺寸)          |
| **典型应用**     | 自动驾驶、视频监控                          | ImageNet分类、迁移学习                  | 肿瘤分割、器官三维重建             |
| **计算复杂度**   | 中 (约50 GFLOPs @640×640)                  | 高 (约4 GFLOPs @224×224)               | 极高 (约500 GFLOPs @128³)         |
| **开源实现**     | [Ultralytics](https://github.com/ultralytics) | [TorchVision](https://pytorch.org/vision) | [MONAI](https://monai.io/)       |  

  
## Transformer
核心：	自注意力、前馈网络
擅长：文本、序列数据，但现在也可用于图像  
O(n^2)全局注意力  
可以完全并行  

Transformer（ViT）：将图像分块为序列，用Transformer处理，挑战CNN的视觉霸权  

可以将transformer理解为动态特征池。当输入图像时，图像会被拆解成一个个拼图块，排列成序列，其中每一个图像块转化为特征向量。使用自注意力机制，每个拼图块自主寻找其他相关块（如猫猫头找猫猫身），通过加权求和重构自身特征。重组后的拼图块被分类头解码为最终结果。（图像中有猫猫）  

CNN的“池”是局部固定的（卷积核尺寸限制），靠深度堆叠扩大感受野。
Transformer的“池”是全局动态的，通过注意力权重直接关联任意距离的元素。


## 常用激活函数和架构使用组合
如果没有激活函数，神经网络无论有多少层，都只能表示线性变换（多个线性层的叠加仍是线性的），无法解决非线性问题（如异或分类、图像识别等）。
激活函数（如ReLU、Sigmoid）可以对输入进行非线性变换，使神经网络可以拟合任意复杂的函数。没有非线性，神经网络就失去了解决复杂问题的能力。  
- CNN中ReLU能帮助模型捕捉图像的层次化特征。


## 参考内容
https://github.com/scutan90/DeepLearning-500-questions/blob/master/ch03_%E6%B7%B1%E5%BA%A6%E5%AD%A6%E4%B9%A0%E5%9F%BA%E7%A1%80/%E7%AC%AC%E4%B8%89%E7%AB%A0_%E6%B7%B1%E5%BA%A6%E5%AD%A6%E4%B9%A0%E5%9F%BA%E7%A1%80.md  

https://github.com/scutan90/DeepLearning-500-questions/blob/master/ch05_%E5%8D%B7%E7%A7%AF%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C(CNN)/%E7%AC%AC%E4%BA%94%E7%AB%A0_%E5%8D%B7%E7%A7%AF%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C(CNN).md  
