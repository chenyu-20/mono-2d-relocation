# mono-2d-relocation  
对于大型平面场景进行手持手机的人员的地面位置定位，实际要求解的是可以进行图片拍摄的手机的位置，不需要很高精度，达到米级即可。  
相机重定位大致有三种，基于2d-3d匹配的，基于图像检索的，基于深度学习的。基于深度学习的需要大量数据集进行训练，但是在最新技术上训练后可以得到一个4MB的模型用来对一个办公室大小的场景进行精确的建模，当输入一个rgb图像即可进行精确的位置输出，误差大部分在5cm/0.5°以内，已在另一个项目中验证，但构建自己的数据集时，又相当于需要自己建立地图数据集，所以又需要用到slam或sfm来进行建图以获得深度学习需要的数据集。另外两种方法殊途同归，都是提取特征点，描述子。2d-3d匹配要建3d点图，比较繁琐，pass。决定使用基于图像检索的重定位方法。  
基于图像检索重定位方法大致思路：先粗定位寻找最相似图像，再根据相似图像进行位置的精确计算即精定位。  
粗定位大多使用词袋法进行快速匹配，精定位采用pnp。这与vins-mono中进行定位的思想是一致的，所以直接使用现有的/成熟的vins算法。以下是复刻这个项目并制作自己的数据集的方法。

---
终于跑出来了，但不是在Ubuntu20上，上面那教程不行，走了两遍流程稳定的没有轨迹，根本不行，还是在16.04上简单方便，教程http://t.csdn.cn/3Pyy2 。
安装环境： ubuntu16.04+ROS kinetic+opencv3.3.1+eigen3.3.3+ceres solver 1.14 
首先把Ubuntu16.04版本装好。
然后  
一.ROS Kinetic的安装  
1.设置sources.list  
```
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```  
2.设置key  
```
sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
```  
3.更新package  
```
sudo apt-get update
```  
4.安装ROS kinetic完整版  
```
sudo apt-get install ros-kinetic-desktop-full
```  
5.初始化rosdep  
```
sudo rosdep init
rosdep update
```
注意：这一步通常会失败，因为网站的原因，直接跳过即可  
6.配置ROS环境  
```
echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```  
7.安装依赖项  
```sudo apt-get install python-rosinstall python-rosinstall-generator python-wstool bu```
ros安装好后，安装vins所需的库。  
先要下载好安装环境中需要的库的文件再开始编译安装。  
二.opencv3.3.1的安装  
1.下载opencv包  
2.安装pip和numpy
```
sudo apt install python-pip
```
```
pip install numpy
```
3.安装依赖项
```
sudo apt-get install build-essential libgtk2.0-dev libvtk5-dev libjpeg-dev libtiff5-dev libjasper-dev libopenexr-dev libtbb-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev libpng-dev libdc1394-22-dev
```
4.解压opencv安装包，然后进行安装
```
tar -xzvf opencv-3.3.1.tar.gz
cd opencv-3.3.1/
cd ..
mkdir build
cd build
cmake ..
make
sudo make install
```
5.安装完成  

三.eigen3.3.3的安装  
1.安装
```
sudo apt-get install libeigen3-dev
```
四.ceres1.14的安装  
1.github的下载链接  
链接: https://github.com/ceres-solver/ceres-solver/tree/master  
注意（这里的branch要切换到1.14版本）  
2.安装依赖项  
注意：若为Ubuntu14.04则应为libcxsparse3.1.2
```
sudo apt-get install liblapack-dev libsuitesparse-dev libcxsparse3.1.4 libgflags-dev libgoogle-glog-dev libgtest-dev
```
```
sudo apt-get install cmake
```
```
sudo apt-get install libgoogle-glog-dev
```
```
sudo apt-get install libatlas-base-dev
```
```
sudo apt-get install libeigen3-dev
```
3.安装
解压Cere安装包，进入安装目录下：
```
tar zxf ceres-solver-1.14.0.tar.gz
cd ceres-solver-1.14.0
mkdir build
cd build
cmake ..
make
sudo make install
```
安装完成  
五.数据集的下载与运行  
1.下载EuRoC数据集  
可到其官网下载，下载格式为.bag的。  

2.创建ROS工作空间
```
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/src
catkin_init_workspace
```
3.编译VINS(环境需要配置正确)
```
cd ~/catkin_ws/src
git clone https://github.com/HKUST-Aerial-Robotics/VINS-Mono.git
cd ../
catkin_make
```
4.运行VINS  
打开三个终端，  
第一个终端：
```
source ~/catkin_ws/devel/setup.bash
roslaunch vins_estimator euroc.launch
```
第二个终端：
```
source ~/catkin_ws/devel/setup.bash　＃＃///配置环境变量
roslaunch vins_estimator vins_rviz.launch　　＃＃启动rviz
```
第三个终端：
```
source ~/catkin_ws/devel/setup.bash
rosbag play ~/数据集的路径(我的路径是home/yhr/下载/)/MH_01_easy.bag
```
---
依然是做自己的数据集，http://t.csdn.cn/nAZiw ，好消息，数据集做出来了，也正常跑了，坏消息，结果不正常，尤其是轨迹很离谱，乱飞，还没标定，应该要标定改参数。    
