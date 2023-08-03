# mono-2d-relocation  
对于大型平面场景进行手持手机的人员的地面位置定位，实际要求解的是可以进行图片拍摄的手机的位置，不需要很高精度，达到米级即可。  
相机重定位大致有三种，基于2d-3d匹配的，基于图像检索的，基于深度学习的。基于深度学习的需要大量数据集又难以移植，pass掉。另外两种殊途同归，都是提取特征点，描述子。2d-3d匹配要建3d点图，怪麻烦，pass。决定使用基于图像检索的重定位方法。  
基于图像检索重定位方法大致思路：先粗定位寻找最相似图像，再根据相似图像进行位置的精确计算即精定位。  
粗定位打算使用词袋法进行快速匹配，精定位试试pnp。这与vins-mono中定位的思想是一致的，所以直接使用现成的vins算法。以下是复刻这个项目并制作自己的数据集的方法。

---
终于跑出来了，但不是在Ubuntu20上，上面那教程不行，走了两遍流程稳定的没有轨迹，根本不行，还是在16.04上简单方便，教程http://t.csdn.cn/3Pyy2 。
安装环境： ubuntu16.04+ROS kinetic+opencv3.3.1+eigen3.3.3+ceres solver 1.14 
首先把Ubuntu16.04版本装好。
然后  
一.ROS Kinetic的安装  
1.设置sources.list  
`sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'`  
2.设置key  
`sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654`  
3.更新package  
`sudo apt-get update`  
4.安装ROS kinetic完整版  
`sudo apt-get install ros-kinetic-desktop-full`  
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
`sudo apt-get install python-rosinstall python-rosinstall-generator python-wstool bu`  

---
依然是做自己的数据集，http://t.csdn.cn/nAZiw ，好消息，数据集做出来了，也正常跑了，坏消息，结果不正常，尤其是轨迹很离谱，乱飞，还没标定，应该要标定改参数。    
