# 坐标系转换笔记
# 相机模型、参数和各个坐标系(世界坐标系、相机坐标系、归一化坐标系、图像坐标系、像素坐标系之间变换）

简单语言：**世界坐标系**通过平移旋转得到**相机坐标系**，**相机坐标系**通过相似三角形（满足小孔成像模型）得到**图像坐标系**，**图像坐标系**通过平移和缩放得到**像素坐标系**  。

常见坐标系：
- 世界坐标系（Xw,Yw,Zw）
- 相机坐标系（Xc,Yc,Zc）
- 归一化坐标系(Xn,Yn)，范围不一定在(-1,1)中，主要操作是去除Zc
<img width="282" height="78" alt="image" src="https://github.com/user-attachments/assets/78584eb2-f41c-4193-946d-6c332fb60c32" />

- 图像坐标系(x,y)物理长度为单位，通常原点在图像中心
- 像素坐标系(u,v)像素为单位，通常原点在左上角

世界坐标系  --[外参 R, t]-->  相机坐标系  
相机坐标系  --[透视投影除Zc]-->  归一化坐标系  
归一化坐标系 --[乘焦距 f, 传感器物理尺寸]--> 图像坐标系  
图像坐标系  --[像素缩放 + 主点平移]--> 像素坐标系  

# 世界坐标系 ——> 像素坐标系
## 世界坐标系到相机坐标系
<img width="547" height="470" alt="image" src="https://github.com/user-attachments/assets/040eb637-27ba-450e-997e-cd1baaa13fed" />  
<img width="660" height="682" alt="image" src="https://github.com/user-attachments/assets/77fd058b-3069-42cb-98f8-95cf61b15983" />

## 相机坐标系到图像坐标系
### 包括归一化步骤
<img width="444" height="234" alt="image" src="https://github.com/user-attachments/assets/3e0d830d-0557-4098-942c-5290cb63e8bf" />  
<img width="693" height="169" alt="image" src="https://github.com/user-attachments/assets/f3d67c30-89df-445c-8704-74acb8b638d0" />

## 图像坐标系到像素坐标系
由于图像坐标系和像素坐标系**处于同一平面**，故两者之间的差异在于坐标原点的位置和单位。像素坐标系的原点在图像坐标系的左上角，同时像素坐标系的单位为像素。
<img width="750" height="412" alt="image" src="https://github.com/user-attachments/assets/4470d90d-3841-4828-bf19-222712db28b8" />  
<img width="688" height="378" alt="image" src="https://github.com/user-attachments/assets/ad556e29-f4aa-428e-a0a0-c766d42fae0e" />

# 像素坐标系 ——> 世界坐标系
## 像素坐标系到图像坐标系
像素坐标系转为图像坐标系，一位两个坐标系同属于一个平面，仅单位不同，原点位置不同。因此只需要进行缩放和平移。

## 图像坐标系到归一化坐标系
图像坐标系单位(x,y)，除焦距f，得到归一化坐标系
<img width="159" height="127" alt="image" src="https://github.com/user-attachments/assets/d82f26ed-9309-4d3c-ab77-af8f537e8e8d" />


## 归一化坐标系到相机坐标系
归一化坐标系到相机坐标系的转换中缺少Zc，关键的深度信息，表示该点到相机光心的距离。  
双目可以通过视差计算深度信息
<img width="231" height="87" alt="image" src="https://github.com/user-attachments/assets/648df51a-b108-43cc-bc53-9296732207dd" />  
- f = 焦距（像素单位）
- B = 双目基线（两个相机之间的距离）
- d = 左右像素坐标差（视差）

深度相机可以直接获取Zc深度

单目相机需要通过假定平面法/PnP
<img width="240" height="104" alt="image" src="https://github.com/user-attachments/assets/3599aab2-bfc7-4f26-9adf-b418f8b759f7" />
- 假定平面法
  - 假设目标点落在已知平面（例如地面）上，则这个平面的方程已知。法向量n=(0,1,0):y轴向上
    - 然后图像坐标系上一点穿过相机光心射线与平面相交，交点可以得到x和z坐标，一般y为0（高度为0），
    - <img width="871" height="331" alt="image" src="https://github.com/user-attachments/assets/a62d9778-8831-4c38-977f-967f9cd7369d" />
    - <img width="696" height="439" alt="image" src="https://github.com/user-attachments/assets/a6645529-5be4-4d28-aa95-55f25e238467" />
    - <img width="637" height="365" alt="image" src="https://github.com/user-attachments/assets/509363cf-4ab4-48de-9d7d-62977f34eb1e" />
  - 或是使用ransac、最小二乘
  - 将像素坐标转成相机坐标系的一条射线（方向向量）
  - 计算该射线与已知平面的交点 → 得到 Zc
- PnP
  - 已知场景中若干已知 3D 点的像素位置，通过求解外参来推导 Zc

## 相机坐标系到世界坐标系
旋转+平移
<img width="330" height="135" alt="image" src="https://github.com/user-attachments/assets/202b1f3a-fbf4-4447-b153-1202e8683161" />  
R是3*3矩阵，T是3*1矩阵，[R|T]矩阵是4*4


# 代码
## 相机坐标系转机器人坐标系
```cpp
while (!estimator_inited_tf)
{
    Eigen::Matrix4f camera2Robot_ = Eigen::Matrix4f::Identity(); /*相机（点云相机坐标系）到机器人坐标系变换矩阵*/
    auto tf_result = get_transform("base_link1", "camera_link", ros::Time(), ros::Duration(0.05));
    // 循环等待，直到能拿到 "camera_link" → "base_link1" 的 TF 变换
    if (tf_result)
    {
        float tx_, ty_, tz_;
        float r00, r01, r02;
        float r10, r11, r12;
        float r20, r21, r22;
        auto tf_result_valse = tf_result.value();
        auto rotation = tf_result_valse.transform().rotation();

        //translation (tx, ty, tz)：相机坐标系原点在机器人坐标系下的位置
        tx_ = tf_result_valse.transform().translation().x();
        ty_ = tf_result_valse.transform().translation().y();
        tz_ = tf_result_valse.transform().translation().z();
        tf2::Quaternion quaternion(rotation.x(), rotation.y(), rotation.z(), rotation.w());
        // 把四元数转为3x3的旋转矩阵
        tf2::Matrix3x3 rotation_matrix(quaternion); // rotation_matrix[0][0]
        // 标准4x4旋转矩阵 R T 0 1：R = 相机坐标系相对于机器人坐标系的旋转矩阵；t = 相机坐标系原点在机器人坐标系下的位置
        camera2Robot_ << rotation_matrix[0][0], rotation_matrix[0][1], rotation_matrix[0][2], tx_,
                        rotation_matrix[1][0], rotation_matrix[1][1], rotation_matrix[1][2], ty_,
                        rotation_matrix[2][0], rotation_matrix[2][1], rotation_matrix[2][2], tz_,
                        0, 0, 0, 1.0;
        m_charge_pose_estimator.m_params.cameraleft_zrobot = camera2Robot_;
        estimator_inited_tf = true;
    }
    std::this_thread::sleep_for(std::chrono::milliseconds(5));
}
auto rgbimage = cv_rgb->image.clone();
```

```cpp
// 代码实现四元数转3x3旋转矩阵
Eigen::Matrix3f quaternionToRotation(float x, float y, float z, float w)
{
    Eigen::Matrix3f R;
    R << 1 - 2 * (y * y + z * z),     2 * (x * y - z * w),     2 * (x * z + y * w),
         2 * (x * y + z * w),     1 - 2 * (x * x + z * z),     2 * (y * z - x * w),
         2 * (x * z - y * w),         2 * (y * z + x * w), 1 - 2 * (x * x + y * y);
    return R;
}
```


# 参考内容
相机模型中四个坐标系的关系：
https://zhuanlan.zhihu.com/p/125006810  
视觉SLAM十四讲
