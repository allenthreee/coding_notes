# Deep3DBox

## 概括

文章中的网络实现了从单目图像预测目标物体3D位置、大小以及朝向的功能。整个算法框架分为三个部分：**2D图像目标检测网络+目标大小姿态估计网络+目标3D中心点解算模块**。

2D图像目标检测网络可以用经典的Faster RCNN、SSD以及YOLO系列，文中采用的是[MS-CNN](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/1607.07155.pdf)。获得目标的2D框之后，将它截取出来，把这些小框resize到一定大小，送进VGG网络来回归3D包围框的长L宽W高H以及朝向alpha。目标3D中心点解算模块就是用预测的HWL以及角度来计算3D包围框中心点的三维坐标（相机坐标系）。

## 1.二维图像目标检测

## 2.大小姿态估计

这部分相对简单，就是在网上找个目标检测网络源码，在后面加回归分支就好了。同时在数据输入模块加上导入目标大小、朝向lable的代码。

这里需要注意一下，朝向用kitti数据集label格式里的第4个参数来表示，即alpha，不要用最后的角度参数r_y（关于这些角度的定义，大家自行查一下吧）。

因为alpha与观察者的视角有关，而r_y木有，下图中车的r_y基本是相同的，但随着观察者的相对位置变化，alpha是变的，对应图像中看到车的不同“样子”，即不同特征，所以用alpha角才合理。我在复现过程中由于不仔细，一开始就直接回归r_y，效果非常差。

![img](https://pic3.zhimg.com/80/v2-feca9725b46bdcce267b65f8a03f57f2_720w.jpg)

还有一个注意点就是，加回归角度的分支时，实际是回归角度的正弦值以及余弦值，不要忘了在fc层后加上L2 norm，因为这样才归一化到三角函数的值域范围。



# Tips

## 交叉熵损失函数

叉熵损失函数[Cross Entropy Loss]的公式：



![[公式]](https://www.zhihu.com/equation?tex=L%3D-%5Bylog%5C+%5Chat+y%2B%281-y%29log%5C+%281-%5Chat+y%29%5D)

从图形的角度，分析交叉熵函数单个样本的交叉熵损失函数： 

当 y = 1 时：

![[公式]](https://www.zhihu.com/equation?tex=L%3D-log%5C+%5Chat+y) 

这时候，L 与预测输出的关系如下图所示：

![img](https://pic1.zhimg.com/v2-8d5eac16bfc171869da269efdcddcc24_b.jpg)



看了 L 的图形，简单明了！横坐标是预测输出，纵坐标是交叉熵损失函数 L。显然，预测输出越接近真实样本标签 1，损失函数 L 越小；预测输出越接近 0，L 越大。因此，函数的变化趋势完全符合实际需要的情况。

当 y = 0 时：

![[公式]](https://www.zhihu.com/equation?tex=L%3D-log%5C+%281-%5Chat+y%29) 

这时候，L 与预测输出的关系如下图所示：

![img](https://pic3.zhimg.com/v2-06cdd7ba5d82d78d784a52ccdcd633ae_b.jpg)

同样，预测输出越接近真实样本标签 0，损失函数 L 越小；预测函数越接近 1，L 越大。函数的变化趋势也完全符合实际需要的情况。

从上面两种图，可以帮助我们对交叉熵损失函数有更直观的理解。无论真实样本标签 y 是 0 还是 1，L 都表征了预测输出与 y 的差距。

另外，重点提一点的是，从图形中我们可以发现：预测输出与 y 差得越多，L 的值越大，也就是说对当前模型的 “ 惩罚 ” 越大，而且是非线性增大，是一种类似指数增长的级别。这是由 log 函数本身的特性所决定的。这样的好处是模型会倾向于让预测输出更接近真实样本标签 y。



## softmax

**softmax用于多分类过程中**，它将多个神经元的输出，映射到（0,1）区间内，可以看成概率来理解，从而来进行多分类！

假设我们有一个数组，V，Vi表示V中的第i个元素，那么这个元素的softmax值就是

![img](https://pic3.zhimg.com/v2-39eca1f41fe487983f5111f5e5073396_b.jpg)![img](https://pic3.zhimg.com/80/v2-39eca1f41fe487983f5111f5e5073396_720w.jpg)

更形象的如下图表示：

![img](https://pic1.zhimg.com/v2-87b232ab0e292a536e94b73952caadd0_b.jpg)

s**oftmax直白来说就是将原来输出是3,1,-3通过softmax函数一作用，就映射成为(0,1)的值，而这些值的累和为1（满足概率的性质），那么我们就可以将它理解成概率，在最后选取输出结点的时候，我们就可以选取概率最大（也就是值对应最大的）结点，作为我们的预测目标！**





## KITTI参数

共有16个参数

### 1.string-物体类别

第1个字符串：代表物体类别

'Car', 'Van', 'Truck','Pedestrian', 'Person_sitting', 'Cyclist','Tram', 'Misc' or 'DontCare'

注意，’DontCare’ 标签表示该区域没有被标注，比如由于目标物体距离激光雷达太远。为了防止在评估过程中（主要是计算precision），将本来是目标物体但是因为某些原因而没有标注的区域统计为假阳性(false positives)，评估脚本会自动忽略’DontCare’ 区域的预测结果。

### 2.int-物体是否被截断

从0（非截断）到1（截断）浮动，其中truncated指离开图像边界的对象

### 3.int-物体是否被遮挡

整数0，1，2，3表示被遮挡的程度

0：完全可见 1：小部分遮挡 2：大部分遮挡 3：完全遮挡（unknown）

### 4.alpha物体的观察角度[-pi~pi]

是在相机坐标系下，以相机原点为中心，相机原点到物体中心的连线为半径，将物体绕相机y轴旋转至相机z轴，此时物体方向与相机x轴的夹角

![img](https://img-blog.csdnimg.cn/20181204101648283.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2N1aWNodWFuY2hlbjMzMDc=,size_16,color_FFFFFF,t_70)

**r_y + pi/2 -theta = alpha +pi/2（即图中紫色的角是相等的）**

**所以alpha = r_y - theta**

### 5~8.物体的二维边框

第5～8这4个数：物体的2维边界框

xmin，ymin，xmax，ymax

### 9~11.三维物体的尺寸

高、宽、长（单位：米）

### 12~14.三维物体的位置

x,y,z（在照相机坐标系下，单位：米）

### 15.三维物体的空间方向

rotation_y

在照相机坐标系下，物体的全局方向角（物体前进方向与相机坐标系x轴的夹角），范围：-pi~pi

### 16.检测置信度







