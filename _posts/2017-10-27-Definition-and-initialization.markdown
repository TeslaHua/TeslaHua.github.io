---
layout: post
category: "slam"
title:  "C++中的顺序容器详解之定义与初始化"
tags: [C++,顺序容器,定义,初始化]
---

### 1.顺序容器的定义与初始化

  每个容器类型都定义一个默认构造函数。**除了array之外**，其他容器的默认构造函数都会创建一个指定类型的空容器，且都可以接受指定容器大小和元素初始值的参数。

  ![definition](https://thumbnail0.baidupcs.com/thumbnail/9504bdc125dd5c34e4ecbc13b3727226?fid=559684340-250528-515843125575661&time=1509177600&rt=pr&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-xmdNv%2b15GIKpQpypiYRC90ecMN0%3d&expires=8h&chkbd=0&chkv=0&dp-logid=6973978742752122044&dp-callid=0&size=c10000_u10000&quality=90&vuk=559684340&ft=image)

<!-- more -->

#### (1)将一个容器初始化为另一个容器的拷贝

  将一个容器初始化为另一个容器的拷贝有两个方法：可以直接拷贝整个容器，**要求两个容器的类型以及元素的类型必须匹配**；或者（array除外）拷贝由一个迭代器对指定的元素范围，不要求两个容器类型与两个容器中的元素类型相同，**只要能将要拷贝的元素转换为要初始化的容器的元素类型即可： **

    list<string>authors = {"Milton","Shakespeare","Austen"};
	vector<const char*>articles = {"a","an","the"};

    list<string>list2(authors);  //正确：类型匹配
	deque<string>authList(authors);  //错误：容器类型不匹配

    vector<string>words(articles);  //错误：容器类型不匹配

    //正确：可以将const char*元素转换为string
    forward_list<string>words(articles.begin(),articles.end());

  可以用这种构造函数来拷贝一个容器中的子序列，例如：假定it表示authors中的一个元素，我们可以编写如下代码：

    //拷贝元素，直到（但不包括）it指向的元素
    deque<string> authList(authors.begin(),it)

### (2)通过列表显示的初始化

    //每个容器有三个元素，用给定的初始化器进行初始化
    list<string>authors = {"Milton","Shakespeare","Austen"};
	vector<const char*>articles = {"a","an","the"};

   对于除了array之外的容器类型，初始化列表还隐含的指定了容器的大小：容器将包含和初始值一样多的元素。

### (3)与顺序容器大小相关的构造函数

  顺序容器（array除外）还提供了另一个构造函数，它接受一个容器大小和一个（可选的）元素初始值

    vector<int> ivec(10,-1);  //10个int元素，每个都初始化为-1
    list<string> svec(10,"hi！"); //10个string,每个都初始化为"hi!"
    forward_list<int> ivec(10);  //10个元素，每个都初始化为0
    deque<string> svec(10);   //10个元素，每个都是空string

### 2.容器类型成员

  ![name](https://thumbnail0.baidupcs.com/thumbnail/79ce55ab7b186c988895bfcef800da06?fid=559684340-250528-361631656921207&time=1509181200&rt=pr&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-FhrL7dcXqZkFDtPzTDz5EFbudr8%3d&expires=8h&chkbd=0&chkv=0&dp-logid=6974474355707502417&dp-callid=0&size=c10000_u10000&quality=90&vuk=559684340&ft=image)

  为了使用这些类型，我们必须显式的使用其类名：
    //iter式通过list<string>定义的一个迭代器类型
    list<string>::iterator iter;
    //count是通过vector<int>定义的一个difference_type的类型
	vector<int>::difference_type_type count;



  

  


  





