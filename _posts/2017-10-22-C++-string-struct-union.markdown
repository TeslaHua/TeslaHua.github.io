---
layout: post
category: "slam"
title:  "C++中的String类、结构体和共用体"
tags: [C++,String,struct,union]
---

### 1. C++中的String类

#### (1)字符串的初始化

	 char first_date[] = {" aaaaaaaa "};
	 char second_date[] {" bbbbbbbbb "};
	 string third_date = {" ccccccccc "};
	 string fourth_date {" ddddddddd "};

<!-- more -->

#### (2)字符串赋值

   **不能讲一个字符数组直接复制给另一个字符数组，但是可以把一个string对象赋给另一个string对象**

     char charr1[20];
	 char charr2[20] = {" bbbbbbbbb "};
	 string str1;
	 string str2 = {" ddddddddd "};
     charr1 = charr2;  //invalid
     str1 = str2;      //valid

#### (3)字符串拼接和附加

     string str3;
     str3 = str1 + str2;  //拼接
     str1 += str2;        //附加

     使用C-风格字符串时，需要使用的函数如下：
     strcpy(charr1,charr2);  //复制
     strcat(charr1,charr2);  //附加

     确定字符串中字符的个数的方法：

     int len1 = str1.Size();
     int len2 = strlen(charr1);

#### (4) 获取输入的一行数据
   
     cin.getline(charr,20); //将一行输入读取到数组中

     getline(cin,str);     //将一行输入读取到string对象中

### 2. C++中的struct结构体

#### （1）结构体的定义与变量声明分离
   
    struct inflatable{
       char name[20];
       float volume;
       double price;
    };

    inflatable duck = {"Daphne",0.12,9.98};    //初始化

#### （2）结构体的定义与变量声明同时(不推荐)
 
    struct inflatable{
       char name[20];
       float volume;
       double price;
    } duck = {
      "Daphne",
       0.12,
       9.98
    }

### 3.C++中的共用体

    union one4all{
       int int_val;
       long long_val;
       double double_val;
    };

#### (1)匿名共用体

    struct widget{
      char brand[20];
      int type;

      union{    //一个匿名共用体变量
        long id_num;
        char id_char[20];
      };
    };
    ...
    widget price;
    ...
    if(price.type == 1)
       cin>>price.id_num;
    else
       cin>>price.id_char;

#### (2)共用体和结构体的区别

  结构体里面的成员都有自己的内存空间，但是union里面的成员共用一个内存空间（且是成员里面的需要内存最大的那个），同时只能调用一个成员。





    




  

   



   
 


 














