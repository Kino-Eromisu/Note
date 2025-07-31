# 笔记

## 参考资料
https://blog.csdn.net/qq_36816848/article/details/122286610?fromshare=blogdetail&sharetype=blogdetail&sharerId=122286610&sharerefer=PC&sharesource=weixin_61044335&sharefrom=from_link


## 核心概念
- **定义**：通过多层神经网络自动学习特征的机器学习方法
- **三大基石**：
  1. 神经网络结构设计
  2. 损失函数
  3. 优化算法

## 基础模型
### 1. 卷积神经网络(CNN)
- **典型结构**：
  `输入 → [Conv → ReLU → Pooling]×N → 全连接层`
- **经典案例**：
  - LeNet-5：手写数字识别(MNIST)
  - ResNet-50：ImageNet分类

### 2. 循环神经网络(RNN)
- **典型结构**：
  `h_t = tanh(W·[h_{t-1}, x_t] + b)`
- **经典案例**：
  - LSTM：文本生成/股票预测

### 3. Transformer
- **核心公式**：
  `Attention(Q,K,V)=softmax(QK^T/√d_k)V`
- **经典案例**：
  - BERT：文本分类
  - ViT：图像分类

## 关键组件
### 激活函数
| 函数   | 公式         | 使用场景          |
|--------|--------------|-------------------|
| ReLU   | max(0,x)     | 最常用，CNN/全连接|
| Sigmoid| 1/(1+e^{-x}) | 二分类输出层      |

### 优化算法
- **SGD**：基础优化器
- **Adam**：默认首选(学习率自适应)

## 实用技巧
1. **数据增强**：
   - 图像：旋转/翻转(MNIST)
   - 文本：同义词替换
2. **正则化**：
   - Dropout(p=0.5)
   - 早停法

## 代码模板(PyTorch)
```python
# CNN示例
model = nn.Sequential(
    nn.Conv2d(3, 16, 3),  # 输入通道3，输出16
    nn.ReLU(),
    nn.MaxPool2d(2),
    nn.Flatten(),
    nn.Linear(16*13*13, 10)  # MNIST分类
)
