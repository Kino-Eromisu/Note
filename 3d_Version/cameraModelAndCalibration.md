# 相机模型和标定 + 对极几何 笔记

## 一、相机模型
相机模型描述了**三维世界中的点**是如何被投影到**二维图像平面**上的。

### 🔹 1. 针孔相机模型（Pinhole）

假设相机为理想针孔。

#### 投影公式：

\[
s \cdot \begin{bmatrix} u \\ v \\ 1 \end{bmatrix}
= K \cdot [R|t] \cdot \begin{bmatrix} X \\ Y \\ Z \\ 1 \end{bmatrix}
\]
<img width="276" height="121" alt="image" src="https://github.com/user-attachments/assets/6a0ddf1f-547b-4aa7-9682-291633768015" />

- \([X, Y, Z]\)：世界坐标系下的三维点  
- \([u, v]\)：图像中的像素坐标  
- \(K\)：内参矩阵  
- \([R|t]\)：外参（相机的姿态）  
- \(s\)：3D点在相机坐标系中的深度，也可以看作深度Z。由物理投影关系决定。

像素坐标和三维点中最后一个参数1的意义是为了计算齐次，方便计算。
三维点中最后一个1表示这是一个位置点而不是方向向量，方向向量应为0

**本质上就是一个平移和一个旋转。代表外参的物理意义。**

说小孔成像模型是去畸变的，是因为它建立在理想几何光学的基础假设上。一般用来求畸变系数。

从相机坐标系转为像素坐标系：归一化、内参变换（线性缩放+平移）

---

### 🔹 2. 相机内参、外参（Intrinsic、Extrinsic Parameters）

内参：描述相机本身的参数，比如相机的焦距、像素大小。

- **焦距**：fx, fy  
- **主点（图像中心）**：cx, cy  
- **畸变参数**（如果存在）：k1, k2, p1, p2, k3

内参矩阵 \( K \)：

\[
K =
\begin{bmatrix}
fx & 0 & cx \\
0 & fy & cy \\
0 & 0 & 1
\end{bmatrix}
\]

<img width="271" height="103" alt="image" src="https://github.com/user-attachments/assets/1942526c-7475-497a-8cb9-8b47def609de" />


外参：在世界坐标系中的参数，比如相机的位置、旋转方向等。**分为旋转矩阵和平移**

矩阵，旋转矩阵和平移矩阵共同描述了如何把点从世界坐标系转换到摄像机坐标系。

（1）旋转矩阵：描述了世界坐标系的坐标轴相对于摄像机坐标轴的方向。

（2）平移矩阵：描述了在摄像机坐标系下，空间原点的位置。

内外参获取代码：

```cpp
// 获取相机参数
rc = g_Device.getProperty(openni::OBEXTENSION_ID_CAM_PARAMS, (uint8_t*)&cameraParam, &dataSize);

// 相机参数结构体定义
typedef struct OBCameraParams
{
    float l_intr_p[4];   // 左相机内参：[fx, fy, cx, cy]
    float r_intr_p[4];   // 右相机内参：[fx, fy, cx, cy]

    float r2l_r[9];      // 右相机到左相机的旋转矩阵，3x3，按行主序：
                        // [r00, r01, r02;
                        //  r10, r11, r12;
                        //  r20, r21, r22]

    float r2l_t[3];      // 右相机到左相机的平移向量：[t1, t2, t3]

    float l_k[5];        // 左相机畸变参数：[k1, k2, p1, p2, k3]
    float r_k[5];        // 右相机畸变参数：[k1, k2, p1, p2, k3]

    // int is_mirror;     // 镜像
} OBCameraParams;
```

---


## 二、相机标定（Calibration）

标定是指通过图像计算得到相机的内参和外参等参数的过程。
<img width="589" height="390" alt="image" src="https://github.com/user-attachments/assets/e0bf163b-2b73-44b3-9d92-1a538274ffbd" />

### CMOS/CCD 与相机光心的关系

- **CMOS或CCD平面**  
  - 安装在相机内部的图像传感器芯片所在的平面，一般位于镜头后方的成像平面。  
  - 是相机内部的感光芯片，通常是一个平面（二维阵列），位于镜头后面，接收通过镜头聚焦的光线，形成图像。

- **相机光心**  
  - 是相机镜头光线聚焦的中心点，不是物理上实际存在的元件。  
  - 通常被定义为相机的投影中心，光线经过镜头的主点或主光轴的参考点。  
  - 这个点更像是几何光学中的一个理想点。

- **光心到CCD平面的距离**  
  - 等于相机的焦距（focal length）。

- **标定重点**  
  - 定位：相机光心在世界坐标系中的位置和姿态（外参）。  
  - 描述 CMOS（成像平面）相对光心的内参（焦距、主点等）。

### 单目、双目相机标定
- 单目相机标定
  - 计算
    - 内参（焦距、主点、畸变参数）
    - 外参（相机坐标系相对于某个世界坐标系的旋转和平移）
  - 使用
    - 标定板（不同姿态角度）
    - 提取角点，使用 Zhang 标定法或 OpenCV 工具进行非线性优化（如 Levenberg-Marquardt）
  - 得到
    - 相机的内参矩阵、畸变系数、每张图像的外参
- 双目相机标定
  - 计算
    - 左右相机的内参与畸变参数
    - 两相机之间的外参（相对旋转 \( R \) 和平移 \( t \)）
    - 基础矩阵、本质矩阵
  - 使用
    - 分别对左右相机进行单目标定
    - 同时拍摄标定板图像对，提取匹配角点，使用立体标定算法估计相机间关系
  - 得到
    - 左右相机的内参与相对外参
    - 可进行图像矫正、视差图计算和三维重建


## 三、对极几何（Epipolar Geometry）  

对极几何实际上是“两幅图像之间的对极几何”，它是图像平面与以基线为轴的平面束的交的几何（这里的基线是指连接摄像机中心的直线）
对极几何（Epipolar Geometry）描述的是两幅视图之间的内在射影关系，与外部场景无关，只依赖于摄像机内参数和这两幅视图之间的的相对姿态  
= 用两张图像之间的几何约束，把“哪里可能是同一个 3D 点的投影”这一二维搜索问题降为一维搜索，并由此恢复相机位姿或场景三维结构
**对极几何 是一个 框架、结构或模型。它描述的是两个相机视图之间的内在的射影几何关系**

### 约束公式
<img width="241" height="86" alt="image" src="https://github.com/user-attachments/assets/92879700-e4dc-4365-8873-8cf3de5da612" />  

基础矩阵的含义是表达了相机2 对于 相机1的相对位置关系  
- 该直线上的点 点乘上 向量I 后，结果为0.
- 该直线外的点 点乘上 标准化的向量I 后，结果为点到该直线上的距离。
- 已知2维平面上两个不同的点的齐次坐标(x,y,1) 和 (x2,y2,1)，让这两个点作叉乘可以得到经过这两点的直线。

根据齐次坐标的性质：
- 基础矩阵F 乘 相机1上的某个像素点坐标 ，结果为相机2上的 候选点直线I2
- I2直线上的任意一点x2 乘上 向量I2 ，结果为 0

### 推导过程

<img width="977" height="328" alt="image" src="https://github.com/user-attachments/assets/c648ddd8-d9d8-4fd3-87a7-3c000cfabe14" />

### 极线约束（对极约束
使用RANSAC等鲁棒性算法来从所有可能的匹配点中估计出最可靠的 F 矩阵。
如果p和p'是匹配点，用F计算后结果必须为0。
作用：排除错误的匹配点

### 基础矩阵F计算
至少需要八对匹配点（八点算法）
<img width="849" height="547" alt="image" src="https://github.com/user-attachments/assets/e23f7d5c-bfd0-42e8-8f48-dccbd3fcdbf9" />  
基础解系的秩+矩阵E EE 的秩 = 9
由于尺度不确定性和秩约束，各减少一个自由度，9-1-1 = 7。实际上矩阵F仅有七个自由度。

因为齐次矩阵，第九个参数比例缩放无意义，所以-1。
但是不是任意的 3×3 矩阵都可以是基础矩阵，必须满足结构上的约束，即它的秩为 2（rank = 2）。

- 七个自由度下，至少需要八个独立方程才能保证rank(A)=8.因为A为nx9的矩阵
- 若n = 8，且所有方程线性无关，则rank(A)=8，此时有唯一解，f为一条线。
- 若n<8，解不唯一，无法确定F

### 双目极线矫正
**极线矫正的核心目标，是将两个非共面行对齐的图像平面，重投影到同一个平面上，使得一对图像上的对应极线变得完全水平对齐，并且位于相同的行上。**
<img width="693" height="237" alt="image" src="https://github.com/user-attachments/assets/97f61eb9-c87c-4161-9e83-cd01932ab180" />  
应用于：立体匹配与深度估计、三维重建、运动估计、虚拟视点合成

- Fusiello校正法
- Bouguet校正法
- 补充 Hartley法 和 LMedS算法

极线矫正方法主要可以分为两类：基于标定的矫正 和 非标定的矫正。  
基于标定：需要已知相机的内参（焦距、主点）和外参（旋转矩阵 R、平移向量 t）。这是最常用、最稳定、精度最高的方法。（Bouguet校正法）  
核心：将旋转平分，使图像平面共面且平行于基线
<img width="789" height="551" alt="image" src="https://github.com/user-attachments/assets/871a3afa-fef0-4f56-9efc-d03f8af675c0" />
过程原理：  

立体矫正：<img width="399" height="208" alt="image" src="https://github.com/user-attachments/assets/a03cf9a6-4dc7-457e-941b-cc1769086554" />  

双目矫正：  

非标定矫正：在不知道相机内参的情况下，仅通过两幅图像中的匹配点对来估计极线几何关系并进行矫正。（Hartley法）  
核心：通过单应变换将极点映射到无穷远  
<img width="849" height="628" alt="image" src="https://github.com/user-attachments/assets/db021ac1-fa06-48b7-8cd7-89298c17b965" />


## 参考资料
三维视觉：原理与实践(课程笔记-相机模型与标定)：  
https://developer.orbbec.com.cn/v/blog_detail?id=903  

对极几何：  
https://blog.csdn.net/tina_ttl/article/details/52749542?fromshare=blogdetail&sharetype=blogdetail&sharerId=52749542&sharerefer=PC&sharesource=weixin_61044335&sharefrom=from_link
  
https://blog.csdn.net/Bartender_VA11/article/details/136080481?fromshare=blogdetail&sharetype=blogdetail&sharerId=136080481&sharerefer=PC&sharesource=weixin_61044335&sharefrom=from_link  
  
https://en.wikipedia.org/wiki/Eight-point_algorithm


