---
layout: post
category: "slam"
title:  "C++中的顺序容器详解之赋值运算与swap"
tags: [C++,assign,swap]
---

### 赋值和swap

  赋值运算将其左边的容器的全部元素替换为右边容器中元素的拷贝：

	 c1 = c2;  //将c1的内容替换为c2中元素的拷贝
	 c1 = {a,b,c};  //赋值后，c1大小为3

  下表中列出的所有运算均适用于所有的容器：

<!-- more -->

  ![assign](http://oyqo0q1a2.bkt.clouddn.com/assign.png)

#### (1)使用assign(仅顺序容器)

  赋值运算符要求左边和右边的运算对象具有相同的类型。顺序容器（array除外）还定义了一个名为assign的成员，允许从一个不同但相容的类型赋值，或者从容器的一个子序列赋值。例如，我们可以用assign实现将一个vector中的一段char*值赋予一个list中的string:

	list<string> name;
	vector<const char*> oldstyle;

	names = oldstyle;  //错误：容器类型不匹配
	//正确：可以将const char*转换成string
    names.assign(oldstyle.cbegin(),oldstyle.cend());

  assign的第二个版本接受一个整型值和一个元素值，它用指定数目且具有相同给定值的元素替换容器中原有的元素：

    list<string> slist1(1);  //1个元素，为空string
	slist1.assign(10,"Hiya!"); //10个元素，每个都是"Hiya!" 

#### (2)使用swap

  swap用于交换两个相同型容器的内容，调用swap之后，两个容器的元素将会交换。

	vector<string> svec1(10);  //10个元素的vector
	vector<string> svec2(24);  //24个元素的vector
	swap(svec1,svec2);

  之后svec1将包含24个string元素，svec2将包含10个string元素。**(除了array之外，因为array会真正交换它们的元素)**交换两个容器内容的操作会很快，因为元素本身并没有交换，swap只是交换了容器的数据结构。

  ![swap](http://oyqo0q1a2.bkt.clouddn.com/swap.png)






   
 

  




