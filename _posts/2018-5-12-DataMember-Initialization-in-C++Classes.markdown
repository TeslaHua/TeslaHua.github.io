---
layout: post
category: "slam"
title:  "C++类成员的初始化问题"
tags: [C++,类成员,初始化]
---


#### C++类对象的构造顺序是这样的：分配内存-->调用构造函数时，隐式／显式的初始化各数据成员-->在构造函数内部初始化数据成员。


1、类里面的任何成员变量在定义时是不能初始化的。

2、一般的数据成员可以在构造函数中初始化。（构造初始化列表初始化和构造函数体内赋值初始化）

3、const数据成员必须在构造函数的初始化列表中初始化。（道理很简单，const成员第一次数据初始化就是发生在类构造数据初始化时，一旦过了此时就会出现问题）。

4、static在类的定义外面初始化。   

5、数组成员是不能在初始化列表里初始化的。

6、不能给数组指定显式的初始化。

从上面的3和5可以看出：C++里面是不能定义常量数组的！但可定义静态常量数组。

<!-- more -->

**对于1的疑问：**

（1）定义类的时候并没有分配内存,赋值的话值放在哪里呢?

当用类构造对象的时候首先分配内存然后调用构造函数,这时候才可以初始化非静态成员变量.
静态成员变量定义的时候在静态存储区中就分配了内存所以可以初始化

类的定义实际相当与类型声明，并没有分配存储空间，初始化的数据哪里放? 类实例化以后才会有实体存储地址及空间。

（2）类只是一个抽象类型，并不是实体的东西，利用这个抽象类型会实例化成不同的个体，每个个体的特征（数据成员）都不一样，如果在类定义中将特征初始化了，岂不是破坏了抽象性，做了一个面向都一样的模子？ 

**为什么static数据成员一定要在类外初始化？**

这是因为被static声明的类静态数据成员，其实体远在main()函数开始之前就已经在全局数据段中诞生了！其生命期和类对象是异步的，（而且静态语意说明即使没有类实体的存在,其静态数据成员的实体也是存的）这个时候对象的生命期还没有开始，如果你要到类中去初始化类静态数据成员,让静态数据成员的初始化依赖于类的实体,那怎么满足前述静态语意呢?难道类永远不被实例化，我们就永远不能访问到被初始化的静态数据成员吗 

静态成员变量隶属于类，不是某个对象，所以静态成员变量不可能占用某一个对象的存储空间，所以静态成员需要再类外部定义，以便静态成员变量在全局数据区分配其存储空间。 

static const int可以在类里面初始化，因为它既然是const，那程序就不会再去试图初始化。

**Tips:**

1.必须对任何const或引用类型成员以及没有默认构造函数的类 类型 的任何成员 显示地使用初始化列表进行初始化；

2.类成员在定义时是不能被初始化的；

3.类的成员初始化顺序与成员变量在构造函数中的位置选后顺序无关，与成员变量在类中定义的先后顺序有关。

[参考资料1](https://www.cnblogs.com/zhaobinyouth/p/6340008.html) 

[参考资料2](https://blog.csdn.net/bboyfeiyu/article/details/8972038) 


