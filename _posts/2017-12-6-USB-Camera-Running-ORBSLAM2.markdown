---
layout: post
category: "slam"
title:  "外接USB摄像头跑ORB_SLAM2的单目Mono代码"
tags: [catkin_make,Ubuntu,ORBSLAM2,Mono]
---

### 1.检测摄像头是否硬件损坏

插上USB摄像头后，在终端运行cheese(茄子)，如果摄像头正常就会出现图像。

<!-- more -->

### 2.外接的USB摄像头设置

    在用户目录下建立/catkin_ws/src文件夹
	mkdir catkin_ws   //注意这里不要用sudo来创建，否则后来catkin_make的时候出错
	cd catkin_ws
	mkdir src
	cd src
	sudo git clone https://github.com/bosch-ros-pkg/usb_cam.git  
	cd ..
	catkin_make    //开始编译

去/dev下面看一下自己摄像头的设备号，一般自带的摄像头是/dev/video0,外接的摄像头就是/dev/video1.所以如果上述操作正确。现在进去~/catkin_ws/src/usb_cam/launch/下，发现有一个usb_cam_test.launch.

	sudo vim usb_cam-test.launch

确认一下里面的设备名到底是/dev/video0还是/dev/video1. 然后在该目录下发布该launch:

	roslaunch usb_cam-test.launch

**此时，如果一切正常，那就会出现摄像头的图像**

这里是直接发布的launch文件，里面包含了usb摄像头的启动节点node和在显示框显示图像的节点node。也可以直接在~/catkin_ws/devel/lib/usb_cam/发现有一个usb_cam_node的可执行文件，可以只运行usb摄像头的启动节点，不过事先打开一个终端，开启ros,即：

	roscore

然后在~/catkin_ws/devel/lib/usb_cam/下：
	
	./usb_cam_node

但是会发现出现一个警告：

	 Camera calibration file /home/teslahua/.ros/camera_info/head_camera.yaml not found.

这个虽然在仅仅试验ROS下的摄像头运行不受影响，这是摄像头的内部参数以及崎变参数，在后面的跑ORBSLAM2的时候也是需要的。

### 3.摄像头的标定

主要是使用的张正友的摄像头标定，具体方法可参考以下链接：

 [Calibration](http://blog.csdn.net/dcrmg/article/details/52929669)

注意：角点数最好不要选相同的。

可以使用下面的这个4*6的棋盘格：

 ![chess](http://oyqo0q1a2.bkt.clouddn.com/chess.bmp)

### 4.开始安装并编译ORBSLAM2的相关库

这一部分过程大家可以自行百度，先在ORB_SLAM2的文件夹下面编译，然后进入Examples/ROS/ORB_SLAM2/下进行编译运行，**这里如果cmake ..出错，就把添加以下ROS的路径的环境变量，就是export ROS_PACKAGE_PATH=${ROS_PACKAGE_PATH}:/home/ORB_SLAM2/Examples/ROS/.其中home是我存放的根目录。**

**如果还是报错的话，说明可能你添加的环境变量是放到了用户账户里面哪个，切换到root用户，在/root/.bashrc尾部添加export ROS_PACKAGE_PATH=${ROS_PACKAGE_PATH}:/home/ORB_SLAM2/Examples/ROS/.**  

### 5.开始运行ROS下的ORB_SLAM2的Mono

这里有两个方法可用：

#### 1. 在这里可以采用先运行摄像头的node,在运行ORB_SLAM2的node.

#### 2. 还可以把两个node都放在一个launch文件中，一块发布

**无论哪种方法每个node都需要以下参数：1、该node所在的功能包pkg；2、该node的类型type(必须是一个可执行的文件);3、以及运行所需要的参数args**
**这里重点强调一下orbslam2这个node的信息：pkg="ORB_SLAM2"、type="Mono"、args="ORBvoc.txt(词典文件) myMonocularCamera.yaml(里面存放的是我的usb摄像头的内参和崎变参数以及其他的参数)"**

    <launch>
      <node name="usb_cam" pkg="usb_cam" type="usb_cam_node" output="screen" >
        <param name="video_device" value="/dev/video0" />
        <param name="image_width" value="640" />
    	<param name="image_height" value="480" />
    	<param name="pixel_format" value="yuyv" />
    	<param name="camera_frame_id" value="camera" />
    	<param name="io_method" value="mmap"/>
		<!--这个参数比较重要，把/usb_cam/image_raw这个主题映射到/camera/image_raw这个主题--> 
    	<remap from="/usb_cam/image_raw" to="/camera/image_raw" />  
  	 </node>
     <node pkg="ORB_SLAM2" type="Mono" name="ORB_SLAM2_Mono"  args="ORBvoc.txt myMonocularCamera.yaml cwd="node"> </node>
	</launch>

可以在src文件夹下vim ros_mono.cc文件，看到里面： nodeHandler.subscribe("/camera/image_raw", 1, &ImageGrabber::GrabImage,&igb)，订阅的是/camera/image_raw这个节点，所以我们需要topic的映射，真正的图像是通过/usb_cam/image_raw传输的。然后发布：

    roslaunch myMonocularCamera.launch  (这是上述的launch文件)

运行完之后，重新打开一个终端，输入：
	
	rqt_graph

可以看到如下的节点连接图：

![rosgraph](http://oyqo0q1a2.bkt.clouddn.com/rosgraph.png)

最后为了尽快的完成初始化，需要左右移动摄像头。

![map](http://oyqo0q1a2.bkt.clouddn.com/map.png)


### 7.参考链接

[reference](http://blog.csdn.net/goding_learning/article/details/52950993)







   
 

  




