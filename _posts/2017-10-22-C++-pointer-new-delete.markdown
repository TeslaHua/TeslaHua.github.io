---
layout: post
category: "slam"
title:  "C++中的指针初探和内存分配"
tags: [C++,指针,new,delete]
---

### 1. new和delete的使用

#### (1)使用new分配内存

   为一个数据对象（可以是结构也可以是基本类型）获得并指定分配内存的通用格式如下：

    typeName * pointer_name = new typeName;
    
    例如：
    int night;
    int *pt = new int;

   对于指针，new分配出来的内存块通常与常规变量分配的内存块不同。变量night和pt的值都存储在被称为栈（stack）的内存区域，而new从被称为堆(heap)或自由存储区的内存区域分配内存。

<!-- more -->

#### (2)使用delete释放内存

     int * ps = new int;
     ...  //use this memory
     delete ps;
  
这将释放ps指向的内存，但不会删除指针ps本身。ps还可以重新指向另一个新分配的内存块。** new 和 delete一定要配对使用，否则会发生memory leak **

  关于动态数组：

    int * psome = new int[10];
    ... //use this memory
    delete [] psome;

#### (3)使用new和delete时应遵循的规则


  1. 不要使用delete来释放不是new分配的内存
  2. 不要使用delete来释放同一块内存块两次
  3. 对空指针应用delete是安全的 


### 2.指针与数组和字符串

#### (1)数组与指针的关系（int型数组为例）


![array](https://thumbnail0.baidupcs.com/thumbnail/6896e55fd39e2dc51939a70d8d556a06?fid=559684340-250528-248023959876054&time=1508677200&rt=pr&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-Nuczl6O6SRswELIp3Kb54fJA7l0%3d&expires=8h&chkbd=0&chkv=0&dp-logid=6839378555744992542&dp-callid=0&size=c10000_u10000&quality=90&vuk=559684340&ft=image)


第二句相当于int型指针cp指向了数组的第一个元素，所以，打印出的cp是第一个元素的地址；&cp[0]与cp等价；&cp也仍然是个地址；\* cp通过解引用得到了数组的第一个元素；只要指针指向的是数组中的元素（或者数组中尾元素的下一位置），都可以执行下标运算,cp[0]等价于\*(cp+0),故为数组的第一个元素。

#### (2)char型数组与指针的关系


![char](https://thumbnail0.baidupcs.com/thumbnail/3b744ba7ee1ddd77b6a3e19f91a101ee?fid=559684340-250528-219211365400098&time=1508677200&rt=pr&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-1i11GmI7S5UQxuZvjfaVVjeGqt8%3d&expires=8h&chkbd=0&chkv=0&dp-logid=6839378555744992542&dp-callid=0&size=c10000_u10000&quality=90&vuk=559684340&ft=image)


前面一段中，cp被定义为指向“Hello World”的char型指针，“Hello World”是string类型，显然与char型不相吻合，可认为编译器做了以下事情：在常量区分配4个字节，分别放上H,e....d,\0这些字符，然后把H的地址返回给cp。（字符串放在了常量区，是不可修改的，试图修改，运行异常。为了兼容才允许这样写，最好写const char *cp="Hello
 World",这样的话，试图修改cp的内容时，编译时就会报错。）

后面一段char str[]={"Hello World"},相当于在栈顶分配了12个字节，分别放上H,e....d,\0这些字符，等价于char str[]={'H','e'....'d','\0'},但长度不包括\0。按道理cp0应该是输出的首元素的地址，等价于&cp0[0]，但实际却输出整个字符串，这是由cout决定的，若要输出地址，则使用int强制转化为地址即可，即cout<<(int)cp0<<endl;。

### 3.指针与结构体

![struct](https://thumbnail0.baidupcs.com/thumbnail/6e7e6c712fca6c3aad8c9e4edf2e41a5?fid=559684340-250528-635291331831831&time=1508677200&rt=pr&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-8nZfnU1BMAoR9Ou%2bL50TgwDaclA%3d&expires=8h&chkbd=0&chkv=0&dp-logid=6839604348861715603&dp-callid=0&size=c10000_u10000&quality=90&vuk=559684340&ft=image)

另一种访问方法是：如果ps是指向结构体的指针，则\* ps就是被指向的结构体本身，因此（\* ps）即使一个结构体，所以可以通过(*ps).menber来访问该成员变量。

### 4.指针与const

两种方法将const关键字用于指针：

#### (1) 第一种方法是让指针指向一个常量对象，这样可以防止使用该指针来修改所指向的值。

    int age = 39;
    const int * pt = &age;

     *pt += 1;  //INVALID
     cin>>*pt;  //INVALID

     *pt = 20;  //INVALID
     age = 20;  //VALID

pt指向一个const int的常量，因此无法通过pt来修改这个值。即*pt的值为const,不能被修改。pt的声明并不意味着它指向的值实际上是一个常量，而只是意味着对pt而言，这个值是常量。而age不是const,可通过age来修改age的值，不能通过pt指针来修改它。

可以将常规变量的地址赋给常规指针，也可以将常规变量的地址赋给const指针。因此，还有两种可能，即：将const变量的地址赋给指向const的指针、将const的地址赋给常规指针。

    const float g_earth = 9.8;
    const float * pe = &g_earth;  //VALID

    const float g_moon = 1.63;
    float * pm = &g_moon;         //INVALID

对于第一种情况，g_earth和pe都无法修改9.8的值；C++禁止使用第二种情况,因为pm可以
修改g_moon的值，这使得g_moon的const状态很荒谬。

**结论：如果数据类型本身不是指针，则可以将const数据或者非const数据的地址赋给const的指针，但只能将非const数据的地址赋给非const指针，不能将const数据赋给非const指针**

***禁止将常量数组的地址赋给非常量指针将意味着不能将数组名作为参数传递给使用非常量形参的函数***

将指针参数声明为指向常量数据的指针有以下理由：

1. 可以避免由于无意间修改数据而导致编程错误；
2. 使用const使得函数能够处理const和非const实参，否则将只能接受非const数据。

#### (2) 第二种方法是将指针本身声明为常量，防止改变指针指向的位置。

    int age = 39;
    const int * pt = &age;

cinst只能防止修改pt指向的值，而不能防止修改pt的值。可以将一个新地址赋给pt:

    int sage = 80;
    pt = &sage;

但是仍然不能使用pt来修改它指向的值（现在是80）.

使用const的方式使得无法修改指针的值：

    int sloth = 3;
    const int * ps = &sloth;   //指向int常量的指针
    int * const finger = &sloth;  //一个int类型的指针常量

中间的声明不允许使用ps修改sloth的值，但是允许ps指向另一个位置；最后的声明使得finger只能指向sloth,但允许finger修改sloth的值。finger和* ps都是const,但是* finger和ps不是。

还可以声明指向const对象的const指针：

    double trouble = 2.50;
    const double * const stick = &trouble;

这里stick只能指向trouble,而stick不能用来修改trouble的值。简言之，stick和*stick都是const.

![const](https://thumbnail0.baidupcs.com/thumbnail/0b3717568edc1b851521ecb6cfa72c06?fid=559684340-250528-209157451399920&time=1508724000&rt=pr&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-pqfHQYNl95Ihxk%2bpXgDu49vVgGg%3d&expires=8h&chkbd=0&chkv=0&dp-logid=6851977592671006682&dp-callid=0&size=c10000_u10000&quality=90&vuk=559684340&ft=image)





   







    




  

   



   
 


 














