---
layout: post
category: "slam"
title:  "C++中类的声明与定义"
tags: [C++,class,声明，定义]
---

### 1.设计Salas_data类

Salas_data类的接口应该包含以下的操作：
 
     1.一个isbn成员函数，用于返回对象的ISBN编号；
     2.一个combine成员函数，用于将一个Salas_data对象加到另一个对象上；
	 3.一个名为add的函数，执行两个Salas_data对象的加法；
     4.一个read函数，将数据从istream读入到Salas_data对象中；
     5.一个print函数，将Salas_data对象的值输出到ostream.


<!-- more -->
最终的Salas_data类应该如下：

     struct Salas_data{

		//新成员：关于Salas_data对象的操作
        std::string isbn() const{ return bookNo;}
        Salas_data& combine(const Salas_data&);
		double avg_price() const;
		//数据成员
		std::string bookNo;
		unsigned units_sold = 0;
		double revenue = 0.0;
     };

     //Salas_data的非成员接口函数
	   Salas_data add(const Salas_data&,const Salas_data&);
       std::ostream &print(std::ostream&,const Salas_data&);
       std::istream &read(std::istream&,Salas_data&);

#### (1)定义成员函数

   所有的成员函数都必须在类的内部声明，但是成员函数体可以在类的内部也可以在类的外部定义。对于Salas_data类isbn函数定义在类内，而combine和avg_price定义在类外。对于isbn函数，参数列表为空，返回值是一个string对象：

    std::string isbn() const {return bookNo;}

   total是一个Salas_data的对象，当total调用isbn函数的时候total.isbn()，成员函数通过一个名为this的额外的隐式参数来访问调用它的哪个对象（total）。并用请求该函数的对象地址初始化this.例如：

   total.isbn();

   编译器把total的地址传递给isbn的隐式形参this,可以等价的认为编译器将调用重写成：

    Salas_data::isbn(&total) //调用isbn成员时传入了total的地址

  在成员函数内部，我们可以直接调用该对象的成员，就是因为this所指的正是这个对象，任何对类成员的直接访问都被看作是this的隐式引用。如下：

    std::string isbn() const { return this->bookNo;} 

#### (2)引入const成员函数

std::string isbn() const {return bookNo;}，这里的const的作用是修改隐式this指针的类型。默认this的类型是指向类类型**非常量**的常量指针，this的类型是Salas_data *const。尽管this是隐式的，仍然需要初始化。意味着我们不能把this绑定到一个常量对象上，也就是不能在一个常量对象上调用普通的成员函数。

但是子啊isbn的函数体内不会改变this所指的对象，所以把this设置为指向**常量**的指针有助于提高函数的灵活性。

 C++允许把const关键字放在成员函数的参数列表之后。此时const表示this是一个指向常量的指针。使用const的成员函数被称为**常量成员函数**isbn可以读取调用它的对象的数据成员，但是不能写入新值。

 **常量对象、以及常量对象的引用和指针都只能调用常量成员函数**

#### (3)在类的外部定义成员函数

 在类的外部定义成员函数时，成员函数的定义与它的声明匹配。即：返回类型、参数列表和函数名都与类内部的声明保持一致。同时，类外部定义的成员名字必须包含它所属的类名：

    double Salas_data::avg_price() const {
	    if(units_sold)
			return revenue/units_sold;
		else
			return 0;
    }

  函数名Salas_data::avg_price使用作用域运算符来说明如下的事实：定义的一个名为avg_price的函数，并且该函数被声明在类Salas_data的作用域中。

#### (4)定义类相关的非成员函数

  一般来说,如果非成员函数是类接口的组成部分，则这些函数的声明应该与类在同一个头文件内。例如：print函数和read函数。

### 2.构造函数

#### (1)构造函数初始化列表

    Salas_data(const std::string &s,unsigned n,double p):
		bookNo(&s),units_sold(n),revenue(p*n) { }

  其中{ }定义了空的函数体，把新出现的部分称为**构造函数初始值列表**，每个名字后面紧跟括号括起来的成员初始值，不同成员初始值用逗号分隔开。同时也可以在类的外部定义构造函数，只需要声明好作用域即可。

#### (2)class与struct关键字

  唯一的区别是默认的访问权限不一样：当希望定义的类的所有成员是public的时，使用struct;反之，希望成员是private的，使用class.









    




  

   



   
 


 














