# mono-2d-relocation  
对于大型平面场景进行手持手机的人员的地面位置定位，实际要求解的是可以进行图片拍摄的手机的位置，不需要很高精度，达到米级即可。  
相机重定位大致有三种，基于2d-3d匹配的，基于图像检索的，基于深度学习的。基于深度学习的需要大量数据集又难以移植，pass掉。另外两种殊途同归，都是提取特征点，描述子。2d-3d匹配要建3d点图，怪麻烦，pass。决定使用基于图像检索的重定位方法。  

---
基于图像检索重定位方法大致思路：先粗定位寻找最相似图像，再根据相似图像进行位置的精确计算即精定位。  

---
粗定位打算使用词袋法进行快速匹配，精定位试试pnp。

---
