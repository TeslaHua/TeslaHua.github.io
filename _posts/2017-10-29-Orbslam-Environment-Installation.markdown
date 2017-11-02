---
layout: post
category: "slam"
title:  "Ubuntu14.04搭建ORBSLAM的运行环境"
tags: [Ubuntu,indigo,orbslam,opencv]
---

### 1.涉及到的一些软件以及安装过程

    1.ubuntu14.04    //合适的linux系统版本
    2.ROS indigo     //适合linux14.04版本的ROS版本
    3.opencv2.4.9    //适合的opencv版本
    4.g2o            //图优化代码库
    5.DBoW2          //用于回环检测的库
    6.Boost          //一个C++的标准库，用来运行不同的slam版本

<!-- more -->

### 2.开始安装前期软件

#### (1)添加Boost库

    sudo apt-get install libboost-all-dev 

#### (2)安装ROS indigo

  1.首先打开自己的ubuntu,设置里面打开**软件与更新**，进行如下的设置，对于下载源我们可以选择清华的或者是aliyun的：

  ![software](http://oyqo0q1a2.bkt.clouddn.com/software.png)

  然后对于其他软件我们进行如下设置：

  ![othersoftware](http://oyqo0q1a2.bkt.clouddn.com/othersoftware.png)

  2.配置源

  使用ROS官方源：

	sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'

  如果官方源不行的话，尝试使用国内镜像源：

    sudo sh -c '. /etc/lsb-release && echo "deb http://mirrors.ustc.edu.cn/ros/ubuntu/ $DISTRIB_CODENAME main" > /etc/apt/sources.list.d/ros-latest.list'

  3.安装用户密钥
   接着输入如下命令从服务器下载密钥导入到本地（可从Ubuntu知识库中的身份验证页面查看）：

    sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net --recv-key 0xB01FA116

  4.更新安装包

    sudo apt-get update  

  **这里经常会出错，如果这里出错，需要你去检查你的软件与更新里面的配置是否正确**
  
  5.安装ROS包，选择ros-indigo-desktop-full版本

    sudo apt-get install ros-indigo-desktop-full 

  **这里出错的话就要去尝试国内的镜像源**，这里安装成功后会出现如下界面：

  ![indigo](http://oyqo0q1a2.bkt.clouddn.com/indigo.png)

  6.初始化rosdep

    sudo rosdep init
	rosdep update 

  初始化成功后出现如下界面：

  ![rosdep](http://oyqo0q1a2.bkt.clouddn.com/rosdep.png)

  7.环境变量配置

    echo "source /opt/ros/indigo/setup.bash" >> ~/.bashrc
	source ~/.bashrc   //是配置生效

  **运行完这些以后最好去/root/路径下，打开.bashrc文件确认是否配置成功**

  8.安装rosinstall工具

    sudo apt-get install python-rosinstall  

  安装成功后出现以下界面,至此ubuntu下的ROS indigo已经成功安装完成：

   ![rosinstall](http://oyqo0q1a2.bkt.clouddn.com/rosinstall.png)

  9.确认是否安装

     roscore  //输入此代码如果没有出错即安装成功  

#### (3)安装opencv

  1.下载opencv-2.4.9.zip
  2.在解压到/home目录下
  3.进入源码目录，创建release目录

    cd opencv-2.4.9
	mkdir release 

  4.可以看到在OpenCV目录下，有个CMakeLists.txt文件，需要事先安装一些软件

    sudo apt-get install build-essential cmake libgtk2.0-dev pkg-config python-dev python-numpy libavcodec-dev libavformat-dev libswscale-dev  

  5.进入release文件夹(安装OpenCV是所有的文件都会被放到这个release目录下)
  cmake编译OpenCV源码，安装所有的lib文件都会被安装到/usr/local目录下

    cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local ..  

  6.开始安装

	sudo make install 

#### (4)安装g2o的所需包

    sudo apt-get install libblas-dev
    sudo apt-get install liblapack-dev
    sudo apt-get install libeigen3-dev

#### (5)关于DBow2

   DBow2（第三方库）DBow2是一种高效的回环检测算法，DBOW2算法的全称为Bags of binary words for fast place recognition in image sequence，使用的特征检测算法为Fast，描述子使用的是brief描述子，是一种离线的方法。使用了DBow2和DLib库的一些算法，进行位置识别和特征匹配，不需要为它添加其他依赖项。

### 3.开始安装ORBSLAM

#### (1)下载orbslam
  
   进入/home文件夹下执行以下命令：

	git clone https://github.com/raulmur/ORB_SLAM.git ORB_SLAM

#### (2)配置orbslama环境变量

  打开配置ubuntu环境变量的/root/.bashrc文件，在文件最底处添加所示的命令行：

    export ROS_PACKAGE_PATH=${ROS_PACKAGE_PATH}:/home/   /*这里即是你的ORBSLAM的存放路径*/

#### (3)Build g2o

   进入到/home/ORB_SLAM/Thirdparty/g2o/路径下，依次输入以下指令：

   	mkdir build
	cd build
	cmake .. -DCMAKE_BUILD_TYPE=Release
	make 

#### (4)Build DBoW2
   进入到/home/ORB_SLAM/Thirdparty/DBoW2/路径下，依次输入以下指令：

    mkdir build
	cd build
	cmake .. -DCMAKE_BUILD_TYPE=Release
	make 

#### (5)Build ORB_SLAM

  **因为我们使用的是ROS indigo,所以我们需要打开ORB_SLAM的manifest.xml，然后屏蔽掉oponcv2**,然后输入以下指令：

    mkdir build
	cd build
	cmake .. -DROS_BUILD_TYPE=Release
	make

  到这里如果一切正常的话，就说明咱们的orbslam环境已经配好了。

### 4.试跑数据集

  1.首先把数据集下载并解压到/home/ORB_SLAM/的目录下，下载地址是：

 <http://webdiis.unizar.es/~raulmur/orbslam/downloads/Example.bag.tar.gz>　

  2.开启新的终端，执行

    roscore    //启动ros服务


  3.开启新的终端，执行

    export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:(你的ORB_SLAM文件夹绝对路径)#添加环境变量，如果ORB_SLAM位于ROS工作空间的话，则忽略

    roslaunch ExampleGroovyHydro.launch

  4.打开新的终端执行，进入Example.bag所在文件夹，执行

    rosbag play --pause Example.bag  #执行图片发布程序，执行后，按空格键开始运行程序

   相关参考链接：

   <http://www.cnblogs.com/li-yao7758258/p/5906447.html>

   <http://blog.csdn.net/dourenyin/article/details/48055441>




  




   

   
 
  











   
 

  




