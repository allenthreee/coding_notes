# CMake笔记

## 前言：

感觉还是要从原理上一点一点的把这些东西搞明白，不能瞎搞。虽然瞎搞一时之间的效果能能够显现，但是长期来说反而更慢。欲速则不达，因此还是慢慢地把基础打牢。


在CMakeLists.txt 前面加上这句话
terminal 可以输出 checking file path
这样就知道我们在哪个目录下找package了
``` 
set(CMAKE_FIND_DEBUG_MODE 1) 
```
## 报错 
error while loading shared libraries: libxxx.so : no such file or directory
可以试一下 sudo ldconfig，
it updates the cache for the linker in a UNIX environment with libraries found in the path
"/etc/ld.so.conf", sudo ldconfig writes it to "/etc/ld.so.cache"


## 安装指令

以安装PCL-1.10为例

### 1. 默认安装各种包

```cmake
#cmake 默认安装操作
#@todo 好像 会安装到 /usr/local 目录下
tar xvfj pcl-pcl-1.10.tar.gz
mkdir build && cd build
cmake ..
make -j4
sudo make isntall
```

### 2. 安装到指定位置

```cmake
#解压文件  假如有这么一个压缩包   pcl-1.10.tar.gz
tar xvfj pcl-1.10.tar.gz
cd pcl-1.10 && mkdir build && cd build
#生成makefile    
#参数：
#-DCMAKE_BUILD_TYPE=Release/Debug    表示是debug模式还是release模式
#其中的区别还要后面了解一下
#-DCMAKE_INSTALL_PREFIX 表示安装路径
cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/home/allen/my_libs/pcl-1.9-install ..
make -j4
#安装，这个时候上面那个  -DCMAKE_INSTALL_PREFIX 的作用就显示出来了，如果不设置这个参数，默认安装位置应该是在 /usr/local 路径下
#这样多个版本之间就会冲突，你最后就不知道怎么办了
#我之前好像就因为这个重新装了几次系统
#简直不要太傻B
#设置这个之后，就会安装到指定路径下
#然后在project 的 CMakeLists.txt 中调用这个版本的库就要采用不同的 find_package() 方法，就是后面的 config mode，不然得到的就是默认版本的库，程序就编译不通过，很痛苦
sudo make install
```



## find_package()

find_package()的主要作用是在为我们的项目引入外部的依赖包，cmake官方微微们定义了许多的Module，在官网上面有，比如VTK什么的，但是像Eigen，PCL这种就没有，然后我们build from source之后就要这样编译安装，只是我还不太清楚这个安装过程，更多取决于操作系统的性质还是cmake指令什么的。cmake默认安装的操作就是上面这样。然后就会注册给cmake吧(那么如果是通过apt 命令安装的，cmake又是怎么找到这些package的呢)。

 find_package()有两种模式，分别是module mode  和  config mode

### module mode

在这种模式下，cmake find_package() 就会根据 CMAKE_MODULE_PATH 来寻找package，就是默认的package，eg.

```cmake
#我现在这个电脑上的pcl库是 ROS 安装的，然后默认是1.8版本
#当需要的版本不兼容的时候就无法通过
find_package(PCL 1.8 REQUIRED)
```

### config mode

这种模式下，find_package() 就可以快乐地自己指定package，就是我们上面说的自定义安装路径 `-DCMAKE_INSTALL_PREFIX`所设置的。然后我们通过下面的方法调用它：

```cmake


#这里这个路径就是我们上面指定的安装路径
#好像只要文件夹包含 <package_name>Config.cmake 这个文件就可以，不用精确到最后面的路径
#this list(APPEND)  seems dosen't work
#list(APPEND CMAKE_INCLUDE_PATH "/home/my_libs/allen/pcl-1.10/share/pcl-1.10")

#but this works
set(Eigen3_DIR "/home/allen/lib_from_source/eigen/eigen-3.3.1-install/share/eigen3/cmake")
find_package(Eigen3 REQUIRED NO_MODULE)

#这个 NO_MODULE 也很重要，这样才不会用 module mode 去 find_package
find_package (PCL REQUIRED NO_MODULE)

#on the tiny computer
list(APPEND CMAKE_INCLUDE_PATH "/home/allen/libs_not_default/pcl-1.10.1-install")
find_package (PCL REQUIRED NO_MODULE)
```

这就是今天记录的东西，还是比较感动，加油，you can do it, your are going to be a professional coder.


输出package 相关信息
```
# If the package has been found, several variables will
# be set, you can find the full list with descriptions
# in the OpenCVConfig.cmake file.
# Print some message showing some of them
message(STATUS "OpenCV library status:")
message(STATUS "    config: ${OpenCV_DIR}")
message(STATUS "    version: ${OpenCV_VERSION}")
message(STATUS "    libraries: ${OpenCV_LIBS}")
message(STATUS "    include path: ${OpenCV_INCLUDE_DIRS}")
```

















