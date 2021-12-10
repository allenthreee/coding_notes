```cmake
CMake Error at /opt/ros/kinetic/share/eigen_conversions/cmake/eigen_conversionsConfig.cmake:106 (message):
  Project 'eigen_conversions' specifies '/usr/include/eigen3' as an include
  dir, which is not found.  It does neither exist as an absolute directory
  nor in '/opt/ros/kinetic//usr/include/eigen3'.  Ask the maintainer 'Tully
  Foote <tfoote@osrfoundation.org>' to fix it.
Call Stack (most recent call first):
  /opt/ros/kinetic/share/catkin/cmake/catkinConfig.cmake:76 (find_package)
  image_pipeline/depth_image_proc/CMakeLists.txt:4 (find_package)


-- Configuring incomplete, errors occurred!
See also "/home/randoms/Documents/ros/workspace/build/CMakeFiles/CMakeOutput.log".
See also "/home/randoms/Documents/ros/workspace/build/CMakeFiles/CMakeError.log".
Makefile:3692: recipe for target 'cmake_check_build_system' failed
make: *** [cmake_check_build_system] Error 1
Invoking "make cmake_check_build_system" failed
```

ROS catkin_make 找不到eigen3，问题可能是ros默认去 /usr/include/eigen3 寻找，但是我装在 /usr/local/include/eigen3，所以找不到

```cmake
sudo ln -s /usr/local/include/eigen3 /usr/include/eigen3
```



解释一下ln命令什么意思

```c++
sudo ln source_file target_file
```



它的功能是为某一个文件或目录在另外一个位置建立一个同步的链接，类似Windows下的超级链接。

这个命令最常用的参数是-s，具体用法是：
sudo ln -s 源文件 目标文件 





删除链接
**rm -rf  symbolic_name  注意不是rm -rf  symbolic_name/** 

那么上面我就是rm -rf  /local/linkwork

链接有两种，一种被称为硬链接（Hard Link），另一种被称为符号链接（Symbolic Link）。建立硬链接时，链接文件和被链接文件必须位于同一个文件系统中，并且不能建立指向目录的硬链接。而对符号链接，则不存在这个问题。默认情况下，ln产生硬链接。
　　在硬链接的情况下，参数中的“目标”被链接至[链接名]。如果[链接名]是一个目录名，系统将在该目录之下建立一个或多个与“目标”同名的链接文件，链接文件和被链接文件的内容完全相同。如果[链接名]为一个文件，用户将被告知该文件已存在且不进行链接。如果指定了多个“目标”参数，那么最后一个参数必须为目录。
　　如果给ln命令加上- s选项，则建立符号链接。如果[链接名]已经存在但不是目录，将不做链接。[链接名]可以是任何一个文件名（可包含路径），也可以是一个目录，并且允许它与“目标”不在同一个文件系统中。如果[链接名]是一个已经存在的目录，系统将在该目录下建立一个或多个与“目标”同名的文件，此新建的文件实际上是指向原“目标”的符号链接文件。





# 查看rosbag包含的msg及其时间戳

# rqt_bag example.bag

```c
rqt_bag example.bag
```



![](/home/allen/图片/rqt_bag.png)



