---
layout: post
category: "slam"
title:  "C++中引用的相关概念"
tags: [C++,引用]
---

#### 1.引用初探

  引用只是为对象起了一个别名，将声明符写成&d的形式来定义引用类型。例如：

    int ival = 1024;
    int &refVal = ival; //refVal指向ival(是ival的另一个名字)
    int &refVal2;  //报错：引用必须被初始化

    refVal = 2;  //把2赋给refVal指向的对象，此处即是赋给了ival
    int li = refVal; //与li = ival执行结果一样

<!-- more -->

#### 2.对const的引用

  可以把引用绑定到const对象上，就像绑定到其他对象上一样，称之为**对常量的引用**，
对常量的引用不能被用作修改它所绑定的对象：

    const int ci = 1024;
    const int &r1 = ci ;  //正确：引用及其对应的对象都是常量

    r1 = 42;  //错误： r1是对常量的引用
    int &r2 = ci;  //错误：试图让一个非常量的引用指向一个常量对象 

    int i = 42;
    int &r1 = i;   //引用r1绑定对象i
    const int &r2 = i;  //r2也绑定对象i,但是不允许通过r2修改i的值
    r1 = 0;       //r1并非常量，i的值修改为0
    r2 = 0;       //错误：r2是一个常量引用

#### 3.传引用参数

    1. 该函数接受一个int对象的引用，然后将对象的值置为0
    
    void reset(int &i){  //i是传给reset函数的对象的另一个名字
       
		i = 0;  //改变了i所引对象的值   
	}

    2. 使用引用避免拷贝

   编写一个函数比较两个string对象的长度。因为string对象可能会非常长，所以应该尽量避免直接拷贝他们，使用引用形参比较明智。

    bool isShorter(const string &s1,const string &s2){
       return s1.size() < s2.size();
    }

    **如果函数无需改变引用形参的值，最好将其声明为常量引用**

    3. 使用引用形参返回额外信息

   一个函数只能返回一个值，然而有时需要返回多个值，引用形参为我们一次返回多个结果提供了途径。例如定义一个find_char的函数，它返回在string对象中某个指定字符第一次出现的位置，同时也希望返回出现的次数。

    //返回s中c第一次出现的位置索引
    //引用形参occurs负责统计c出现的总次数
    int find_char(const string &s,char c,int &occurs){
       
        auto ret = s.size();  //第一次出现的位置（如果有的话）
        occurs = 0;  //初始化出现的次数
        for(declttype(ret) i=0;i!=s.size();i++){  //declttype与auto搭配，用于查询表达式的数据类型
       		if(s[i] == c){
               if(ret == s.size())
					ret = i;  //记录第一次出现的位置
			   ++occurs;      //出现的次数加1
            }
        }
		return ret;  //出现的次数通过occurs隐式的返回
    }

    


 




    




  

   



   
 


 














