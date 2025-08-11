# 坐标系转换笔记
# 相机模型、参数和各个坐标系(世界坐标系、相机坐标系、归一化坐标系、图像坐标系、像素坐标系之间变换）

简单语言：**世界坐标系**通过平移旋转得到**相机坐标系**，**相机坐标系**通过相似三角形（满足小孔成像模型）得到**图像坐标系**，**图像坐标系**通过平移和缩放得到**像素坐标系**  

## 世界坐标系到相机坐标系
<img width="547" height="470" alt="image" src="https://github.com/user-attachments/assets/040eb637-27ba-450e-997e-cd1baaa13fed" />  
<img width="660" height="682" alt="image" src="https://github.com/user-attachments/assets/77fd058b-3069-42cb-98f8-95cf61b15983" />

## 相机坐标系到图像坐标系
<img width="444" height="234" alt="image" src="https://github.com/user-attachments/assets/3e0d830d-0557-4098-942c-5290cb63e8bf" />  
<img width="693" height="169" alt="image" src="https://github.com/user-attachments/assets/f3d67c30-89df-445c-8704-74acb8b638d0" />

## 图像坐标系到像素坐标系
由于图像坐标系和像素坐标系处于同一平面，故两者之间的差异在于坐标原点的位置和单位。像素坐标系的原点在图像坐标系的左上角，同时像素坐标系的单位为像素。
<img width="750" height="412" alt="image" src="https://github.com/user-attachments/assets/4470d90d-3841-4828-bf19-222712db28b8" />  
<img width="688" height="378" alt="image" src="https://github.com/user-attachments/assets/ad556e29-f4aa-428e-a0a0-c766d42fae0e" />

  

# 参考内容
相机模型中四个坐标系的关系：
https://zhuanlan.zhihu.com/p/125006810
