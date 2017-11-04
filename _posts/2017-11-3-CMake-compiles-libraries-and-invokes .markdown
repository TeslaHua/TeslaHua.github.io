---
layout: post
category: "slam"
title:  "Cmake在一个工程中编译库并链接该库"
tags: [Cmake,Ubuntu,library,make]
---

**提示： 本博客是在一个工程中，生成库文件，并在该工程中链接此库，基于ubuntu系统。**

### 1.起始工程目录

<!-- more -->

	/tmp/test
	│
	├── build              //工程文件在此编译以及中间文件的放置处
	├── bin                //工程的可执行文件.exe放置处
	├── CMakeLists.txt     //整个工程的配置文件
	├── include            //工程的头文件放置处
	│   └── math.h
	├── libs               //库文件夹
	│ 	├── build
	│ 	├── CMakeLists.txt
	│ 	├── include
	│ 	│   └── mymath.h
	│ 	└── src
	│ 	    └── mymath.c
	└── src
	    └── main.c

### 2.工程文件的内容
 
#### (1)libs下的头文件mymath.h

     #ifndef _MYMATH_H_
	 #define _MYMATH_H_
	
	 int add(int a, int b);
	 int sub(int a, int b);
	
	 #endif

#### (2)libs下的源文件mymath.c

    #include "mymath.h"

	int add(int a, int b)
	{
	    return a + b;
	}
	
	int sub(int a, int b)
	{
	    return a - b;
	}

#### (3)工程文件下的头文件math.h

	#ifndef _MATH_H_
	#define _MATH_H_
	
	int add(int a, int b);
	int sub(int a, int b);
	
	#endif  

#### (4)工程下的主函数main.c

	#include <stdio.h>
	
	#include "math.h"
	
	int main()
	{
	    printf("Hello CMake!\n");
	    printf("1 + 5 = %d\n", add(1, 5));
	
	    return 0;
	}

### 3.CMakeLists.txt的编写

#### (1)编写libs下的CMakeLists.txt

	# 版本限定 
	CMAKE_MINIMUM_REQUIRED(VERSION 2.8)                       
	# 项目名称 
	PROJECT(LIBS) 
	# 设置编译器选项：O3优化代码，Wall显示所有警告，march=native自动匹配编译器类型
	SET(CMAKE_C_FLAGS "$ENV{CFLAGS} -O3 -Wall -march=native") 
	# 定义头文件的路径变量
	SET(INCLUDE_PATH ${PROJECT_SOURCE_DIR}/include) 
	MESSAGE(STATUS "Include Path, ${INCLUDE_PATH}") 
	# 定义源文件路径变量
	SET(SOURCE_PATH ${PROJECT_SOURCE_DIR}/src) 
	MESSAGE(STATUS "Source Path , ${SOURCE_PATH}") 
	# 包含头文件路径
	INCLUDE_DIRECTORIES(${INCLUDE_PATH}) 
	# 添加源文件路径下所有源文件存放到变量中(*.c && *.cpp)，当然也可以手动一个个文件添加进来
	AUX_SOURCE_DIRECTORY(${SOURCE_PATH} SRC_LIST) 
	# 设置动态库输出路径，直接输出到libs的根目录中
	SET(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR})  
	MESSAGE(STATUS "Library Output Path, " ${PROJECT_SOURCE_DIR}) 
	# 生成动态库(libmymath.so)
	ADD_LIBRARY(mymath SHARED ${SRC_LIST}) 
	# 生成静态库（libmymath.a，target名字只能有一个，所以不能与动态库的名字一样）
	ADD_LIBRARY(mymath_static STATIC ${SRC_LIST})
	# 但是可以通过下面的命令更改静态库target生成的库名，这样就和动态库的名字一样的了
	SET_TARGET_PROPERTIES(mymath_static PROPERTIES OUTPUT_NAME "mymath") 

#### (2)编写工程下的CMakeLists.txt

	# 版本限定 
	CMAKE_MINIMUM_REQUIRED(VERSION 2.8)     
	# 项目名称 
	PROJECT(TEST)
	# 设置C语言编译器选项：O3优化代码，Wall显示所有警告,march=native自动匹配编译器类型
	SET(CMAKE_C_FLAGS "$ENV{CFLAGS} -O3 -Wall -march=native") 
	# 定义头文件的路径变量
	SET(INCLUDE_PATH ${PROJECT_SOURCE_DIR}/include) 
	MESSAGE(STATUS "Include Path, ${INCLUDE_PATH}")
	# 定义库文件路径
	SET(LIB_PATH ${PROJECT_SOURCE_DIR}/libs)
	MESSAGE(STATUS "Libs Path, ${LIB_PATH}")
	# 定义源文件路径变量
	SET(SOURCE_PATH ${PROJECT_SOURCE_DIR}/src) 
	MESSAGE(STATUS "Source Path , ${SOURCE_PATH}") 
	# 包含头文件搜索路径
	INCLUDE_DIRECTORIES(${INCLUDE_PATH}) 
	# 包含库搜索路径
	LINK_DIRECTORIES(${LIB_PATH})
	# 定义待链接库名字
	# 链接静态库
	SET(LIBS_LIST libmymath.a)                                                                                     
	# 链接动态库
	#SET(LIBS_LIST libmymath.so)
	# 添加源文件路径下所有源文件存放到变量中(*.c && *.cpp)
	AUX_SOURCE_DIRECTORY(${SOURCE_PATH} SRC_LIST)
    #将输出的可执行文件放到工程目录的bin目录下
	SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
	# 设置生成可执行文件的名称
	SET(EXECUTABLE_FILE_NAME mymath.exe)
	# 生成可执行文件
	ADD_EXECUTABLE(${EXECUTABLE_FILE_NAME} ${SRC_LIST})
	# 设置可执行文件链接的库名称
	TARGET_LINK_LIBRARIES(${EXECUTABLE_FILE_NAME} ${LIBS_LIST})

### 4.在ubuntu的终端进行

#### (1)首先进入/tmp/test/libs/build

     cd /tmp/test/libs/build
     cmake ..
     make 

  如果运行正常，你将会在libs文件夹下看到libmymath.a和libmymath.so两个库文件。

#### (2)再进入/tmp/test/build

     cd /tmp/test/build
	 cmake ..
     make

  整个工程就编译、链接完成，查看工程的bin目录下就会看到已经生成了mymath.exe。

#### 5.最终的工程目录

   ![cmake](http://oyqo0q1a2.bkt.clouddn.com/Cmake.jpg)






   
 

  




