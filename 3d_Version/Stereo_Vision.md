# 双目标定+立体校正+双目测距+点云显示

## 三维重建
飞行时间(Time of flight,TOF)，代表公司微软Kinect2，PMD，SoftKinect， 联想 Phab，在手机中一般用于3D建模、AR应用，AR测距(华为TOF镜头)
双目视觉(Stereo Camera)，代表公司 Leap Motion， ZED， 大疆;
结构光(Structured-light)，代表公司有奥比中光，苹果iPhone X(Prime Sense)，微软 Kinect1，英特尔RealSense, Mantis Vision 等，在手机（iPhone，华为）中3D结构光主要用于人脸解锁、支付、美颜等场景。

## 项目结构
.  
├── config       # 相机参数文件  
├── core         # 相机核心算法包  
├── data         # 相机采集的数据  
├── demo         # demo文件  
├── libs         # 第三方依赖包  
├── scripts      # 脚本  
│   ├── mono_camera_calibration.sh     # 单目相机校准Linux脚本  
│   ├── mono_camera_calibration.bat    # 单目相机校准Windows脚本  
│   ├── stereo_camera_calibration.sh   # 双目相机校准Linux脚本  
│   └── stereo_camera_calibration.bat  # 双目相机校准Windows脚本  
├── get_stereo_images.py                      # 采集标定文件  
├── mono_camera_calibration.py                # 单目相机标定  
├── stereo_camera_calibration.py              # 双目相机标定  
└── README.md  

项目依赖包：  
numpy==1.19.5  
matplotlib==3.1.0  
Pillow==6.0.0  
bcolz==1.2.1  
easydict==1.9  
opencv-contrib-python==4.5.4.60  
opencv-python==4.5.1.48  
pandas==1.1.5  
polyaxon-client==0.5.6  
polyaxon-deploy==0.5.6  
polyaxon-dockerizer==0.0.9  
polyaxon-schemas==0.5.6  
PyYAML==5.3.1  
scikit-image==0.17.2  
scikit-learn==0.24.0  
scipy==1.5.4  
seaborn==0.11.2  
tqdm==4.55.1  
xmltodict==0.12.0  
memory_profiler  
open3d-python==0.7.0.0  

## 双目标定校准
1. 双目相机三维重建也可以使用RGB+IR(红外)的摄像头，甚至IR+IR的也是可以
2. 基线不太建议太小，作为测试，一般baseline在3~9cm就可以满足需求，有些无人车的双目基线更是恐怖到1~2米长
3. 从双目三维重建原理中可知，左右摄像头的成像平面尽可能在一个平面内，成像平面不在同一个平面的，尽管可以立体矫正，其效果也差很多。

注意事项：  
需要购买标定板，用A4纸打印的精度不够   
需要适当调整相机焦距  
采集棋盘格图像时，标定板一般占视图1/2到1/3左右  
一般采集15~30张左右  
width和height表示的是相交点个数  

双目标定的目标是获得左右两个相机的内参、外参和畸变系数，其中内参包括左右相机的fx，fy，cx，cy，外参包括左相机相对于右相机的旋转矩阵和平移向量，畸变系数包括径向畸变系数（k1， k2，k3）和切向畸变系数（p1，p2）  
**fx,fy:焦距** = 相机在 x 和 y 方向上的等效焦距 = 物理焦距 * x/y方向像素密度  
**cx, cy:主点坐标**（像素单位） = 相机光轴与图像平面的交点坐标  
**R:旋转矩阵** = 3×3 旋转矩阵（左相机相对于右相机） = 两个相机之间的相对朝向关系 = 正交矩阵，RᵀR = I，det(R) = 1  
**T:平移向量** = 平移向量（左相机相对于右相机）= 左相机在右相机坐标系中的位置 = 两个相机之间的基线距离  
**K1，K2，K3:径向畸变系数** = 描述由于镜头形状引起的图像扭曲 = k < 0 图像边缘向内弯曲 桶型畸变 ，（k > 0）图像边缘向外弯曲 枕型畸变  
**P1，P2:切向畸变系数** = 描述由于镜头与图像传感器不平行引起的畸变  
畸变矫正过程：  
1. 将图像坐标归一化到相机坐标系
2. 应用径向和切向畸变校正
3. 重新投影回图像坐标系

stereo_cam.yml相机参数文件，这个文件，包含了左右相机的相机内参矩阵(K1,K2)，畸变系数矩阵(D1,D2)，左右摄像机之间的旋转矩阵R和平移矩阵T，以及本质矩阵E和基本矩阵F等
标定代码会显示每一张图像的棋盘格的角点检测效果，如果发现有检测不到，或者角点检测出错，则需要自己手动删除这些图像，避免引入太大的误差  
若误差超过0.1，建议重新调整摄像头并标定，不然效果会差很多  


## 参考内容
CSDN:  
https://blog.csdn.net/guyuealian/article/details/121301896?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522fcf425a4c92a1ca9859e12e379bade68%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=fcf425a4c92a1ca9859e12e379bade68&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-2-121301896-null-null.142^v102^pc_search_result_base8&utm_term=%E5%8F%8C%E7%9B%AE&spm=1018.2226.3001.4187  

