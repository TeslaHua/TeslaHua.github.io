---
layout: post
category: "slam"
title:  "常量引用、常量指针和指向常量的指针"
tags: [C++,常量,引用，指针]
---

##常量引用、常量指针和指向常量的指针

### 1.常量和指针

   指针和常量的搭配的主要有以下六种，如下：

	1. const int p;
	2. const int* p;
	3. int const* p;
	4. int * const p;
	5. const int * const p;
	6. int const * const p;

<!-- more -->

   第一种是常量整数，没什么好说的，p的值无法改变。

   后面五种是指针，有一个简便的办法记忆。

   从右往左读，遇到p就替换成“p is a ”遇到*就替换成“point to”。

   比如说2，读作：p is a point to int const. p是一个指向整型常量的指针。

   3读作：p is a point to const int.意思跟2相同，**（p所指的内容不可改变，但是可以让p重新指向另一个内存地址）**。

   4读作：p is a const point to int.**（p是一个常量指针，指向整型，p所在的地址不可以改变，但是p所存的内容可以改变）**。

   5读作：p is a const point to int const.

   6读作：p is a const point to const int.

   5和6的意思相同，p都是常量指针，指向整型常量。**（指针本身和所指对象都是常量，都无法改变）**


### 2.常量和引用 

   指针和引用的区别是：

   1.指针本身就是一个对象，允许对指针赋值和拷贝，而且在指针的生命周期内它可以先后指向几个不同的对象；

   2.指针无须在定义时赋初值。和其他内置类型一样，在块作用域内定义的指针如果没有被初始化，也将拥有一个不确定的值。
 	
   把引用绑定到一个const对象上，称之为对常量的引用，简称**常量引用**，

   初始化和对const的引用：

   **在初始化常量引用时允许用任意表达式作为初值，只要该表达式的结果能够转换成引用的类型即可。尤其，允许为一个常量引用绑定非常量的对象、字面值甚至是一个一般表达式：**

	int i = 42;
	const int &r1 = i;      //允许将const int& 绑定到一个普通int对象上
	const int &r2 = 42;     //正确：r1是一个常量引用
	const int &r3 = r1 *2;  //正确：r3是一个常量引用
	r1 = 42;                //错误：r1是对常量的引用
	int &r4 = r1 *2;        //错误：r4是一个普通的非常量引用


### 3.底层const和顶层const

   （1）用**顶层const表示指针本身时一个常量**，而用**底层const表示指针所指的对象是一个常量**。

   更一般的是顶层const可以表示任意的对象是常量，这一点对任何数据类型都是用；底层const则与指针和引用等复合类型的基本类型部分有关，**指针既可以是顶层const,也可以是底层const。**

	int i = 0;
	int *const p1 = &i;         //不能改变p1的值，这是一个顶层const
	const int ci = 42;          //不能改变ci的值，这是一个顶层const
	const int * p2 = &ci;       //允许改变p2的值，这是一个底层const
	const int *const p3 = p2;   //靠右的const是顶层const,靠左的是底层const
	const int &r = ci;          //用于声明引用的const都是底层const

   （2）区分顶层const和底层const的作用

   1. 执行对象拷贝时有限制，常量的底层const不能赋值给非常量的底层const。也就是说，你只要能正确区分顶层const和底层const，你就能避免这样的赋值错误。下面举一个例子：
  

	int num_c = 3;  
	const int *p_c = &num_c;  //p_c为底层const的指针  
	//int *p_d = p_c;  //错误，不能将底层const指针赋值给非底层const指针  
	const int *p_d = p_c; //正确，可以将底层const指针复制给底层const指针 


   2. 使用命名的强制类型转换函数const_cast时，需要能够分辨底层const和顶层const，因为const_cast只能改变运算对象的底层const。下面举一个例子：
   
    int num_e = 4;  
	const int *p_e = &num_e;  
	//*p_e = 5;  //错误，不能改变底层const指针指向的内容  
	//正确，const_cast可以改变运算对象的底层const。但是使用时一定要知道num_e不是顶层const的类型。  
	int *p_f = const_cast<int *>(p_e);
	*p_f = 5;  //正确，普通指针可以改变指向的内容  
	cout << num_e;  //输出5  
 
### 4.小结

   **指向常量的指针和常量引用一样，没有规定其所指的对象必须是一个常量。所谓指向常量的指针和常量引用仅仅要求不能通过该指针修改对象的值，并没有规定那个对象的值不能通过其他途径修改**

   



