---
layout: post
category: "slam"
title:  "C++中的顺序容器详解之迭代器"
tags: [C++,顺序容器,Iterators]
---

### 1.标准容器迭代器的运算符

  下面列出的是所有标准容器的迭代器拥有的运算符,其中**forward_list迭代器不支持递减运算符(--)**：

  ![yunsuan](http://oyqo0q1a2.bkt.clouddn.com/yunsuan.png)

<!-- more -->

  和指针类似，也能通过解引用迭代器来获取它所指示的与元素：

    string s("some string");
    if(s.begin()!=s.end()){  //确保s非空
		auto it = s.begin();  //it表示s的第一个字符
		*it = toupper(*it);   //将当前字符改成大写形式
	}

### 2.迭代器类型

  那些拥有迭代器的标准库类型使用iterator和const_iterator来表示迭代器的类型：

    vector<int>::iterator it;   //it能读写vector<int>的元素
    string::iterator it2;       //it2能读写string对象中的字符

    vector<int>::const_iterator it3; //it3只能读取元素，不能写元素
	string::const_iterator it4;  //it3只能读取字符，不能写字符

### 3.begin和end运算符

  一个迭代器范围由一对迭代器表示，两个迭代器分别指向同一个容器中的元素或者是尾元素之后的位置，这两个迭代器通常被称为begin和end，我们可以以下面的方式来循环处理一个元素范围：

    [begin,end)   //迭代器范围的左闭合区间

	while(begin != end){
		*begin = val; //正确：范围非空，因此begin指向一个元素
		++begin;      //移动迭代去，获取下一个元素
	}

  begin和end返回的具体类型由对象是否是常量决定，如果对象是常量，begin和end返回const_iterator；如果不是常量，返回iterator:
	
    vector<int> v;
	const vector<int> cv;
	auto it1 = v.begin(); //it1的类型是vector<int>::iterayor
	auto it2 = cv.begin();//it2的类型是vector<int>::const_iterayor

	auto it3 = v.begin(); //it3的类型是vector<int>::const_iterayor

### 4.结合解引用和成员访问操作

  解引用迭代器可获得迭代器所指的对象，如果该对象恰好是类，就有可能希望进一步访问他的成员。例如令it是该vector对象的迭代器，检测it所指的字符串是否为空：
	
	(*it).empty();  //(*it).empty()中的圆括号必不可少

 此句的含义是先对it解引用，然后解引用的结果再执行点运算符。C++定义了箭头运算符（->）。把解引用和成员访问两个操作结合在一起，也就是说：
 **it->mem和(*it).mem表达的意思相同。**

### 5.vector和string、deque和array迭代器特有的运算符

  ![diedai](http://oyqo0q1a2.bkt.clouddn.com/diedai.png)

   
 

  




