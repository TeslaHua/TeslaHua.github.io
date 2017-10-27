---
layout: post
category: "slam"
title:  "C++中类型转换与继承特性"
tags: [C++,OOP,TypeConversions,Inheritance]
---

### 1.基类与派生类的类型转换与继承

  因为在派生类对象中含有与其基类对应的组成部分，所以我们能把派生类的对象当成基类对象来使用，而且我们也能将基类的指针或引用绑定到派生类对象的基类组成。

    Bulk_quote bulk;   //派生类对象

    Quote *p = &bulk;  //正确：p指向bulk的Quote部分
    Quote &r = bulk;   //正确：r绑定到bulk的Quote部分

 将基类的指针或引用绑定到派生类对象上有一层极为重要的含义：

 当使用基类的引用(或指针)时，实际上我们并不清楚该引用(或指针)所绑定是基类的对象还是派生类的对象。

<!-- more -->

#### (1)不存在从基类向派生类的隐式类型转换

 **之所以存在派生类向基类的类型转换是因为每个派生类对象都包含一个基类部分，而基类的引用或指针可以绑定到该基类的部分。** 但是因为一个基类的对象可能是派生类对象的一部分，也可能不是，所以不存在从基类向派生类的自动类型转换。即：

    Quote base;
    Bulk_quote *bulkP = &base;  //错误：不能将基类转换成派生类
    Bulk_quote& bulkRef = base;  //错误： 不能将基类转换成派生类

 如果上述语法合理的话，就可能会使用bulkP或bulkRef访问base中本不存在的成员。除此之外还有一种情况显得有点特别，即使一个基类指针或引用绑定在一个派生类对象上，也不能执行从基类向派生类的转换，即：
 
    Bulk_quote bulk;
	Quote * itemP = &bulk;
    Bulk_quote *bulkP = itemP;  //错误： 不能将基类转换为派生类

#### (2)在对象之间不存在类型转换

 **派生类向基类的自动类型转换只对指针或引用类型有效，在派生类类型和基类类型之间不存在这样的转换。**

    Bulk_quote bulk;
    Quote item(bulk);  //使用Quote::Quote(const Quote&)构造函数
	item = bulk;       //调用Quote::operator-(const Quote&)

 当构造item时，运行Quote的拷贝构造函数，该函数只能处理Quote中的成员，它负责拷贝bulk中的Quote部分的成员，同时忽略掉bulk中Bulk_quote部分的成员。类似的，对于bulk赋值给item的操作来说，只有bulk中Quote部分的成员被赋值给item，**而bulk中Bulk_quote部分的成员将被切掉。**







