---
layout: post
category: "slam"
title:  "C++中派生列表与多态机制"
tags: [C++,Derived,class,多态]
---

### 1.类派生列表和动态绑定

#### (1)类派生列表

 先定义一个Quote类,并将它作为层次关系中的基类，将包含两个成员函数。并派生出一个名为Bulk_quote的类，它表示可以打折销售的书籍：

    class Quote{
	   public:
		 std::string isbn() const;    //返回书籍的ISBN号，不涉及派生类的特殊性，之定义在Quote类中
		 //net_price返回书籍的实际销售价格
	     virtual double net_price(std::size_t n) const; 
	};

<!-- more -->

  在C++中，基类将类型相关的函数与派生类不做改变直接继承的函数区别对待，对于某些函数，基类希望它的派生类各自定义适合自身的版本，此时基类就将这些函数声明为**虚函数(virtual function)**。

 派生类必须通过使用类派生列表指出它是从哪个（哪些）基类继承而来的，格式如下：

    class Bulk_quote: public Quote {
	   public:
		    //重新定义了虚函数net_price
			double net_price(std::size_t) const override;
       private: 
      		std::size_t min_qty = 0;
			double discount = 0.0;
	};

  因为我们完全可以把Bulk_quote的对象当成Quote的对象来使用。其中派生类必须在其内部对所有重新定义的虚函数进行声明。	允许派生类显示的注明哪个成员函数改写基类的虚函数，即在该函数的形式列表之后增加一个**override**关键字。

  **注意： 派生类中重新定义的虚函数必须与基类的该函数的参数列表以及返回值一致**

  **派生类构造函数：**

     Bulk_quote(const std:: string& book,double p,std::size_t qty,double disc):
       Quote(book,p), min_qty(qty),discount(disc) {}

   该函数将它的前两个参数book、p传递给Quote的构造函数，由Quote的构造函数负责初始化Bulk_quote的基类部分（即bookNo成员和price成员）————>
   当(空的)Quote构造函数体结束后，构建对象的基类部分也就完成初始化了————>
   初始化由派生类直接定义的min_qty成员和discount成员————>
   运行Bulk_quote构造函数的（空的）函数体。

#### (2)动态绑定
 
  通过动态绑定可以用同一段代码分别处理Quote和Bulk_quote的对象，例如：

    //计算并打印销售给定数量的某种书籍所得到的费用
    double print_total(ostream &os,const Quote &item,size_t n)
	{
		//根据传入的item形参的对象类型调用Quote::net_price
        //或者Bulk_quote::net_price
		double ret = item.net_price(n);   
        //调用Quote::isbn
		os << "ISBN: "<< item.isbn() <<" total due: "<<ret <<endl;
		return ret;
	}

  **结论：**

   1.print_total的item形参是基类Quote的一个引用，所以既能使用基类Quote的对象也能用派生类Bulk_quote的对象调用它；
   
   2.实际传入的print_total的对象类型将决定到底执行net_price的哪个版本：

     //basic对象是Quote类型的；bulk对象是Bulk_quote类型的
     //print_total(cout,basic,20); //调用Quote的net_price
	 //print_total(cout,bulk,20); //调用Bulk_quote的net_price

  **在C++语言中，当我们使用基类的引用(或指针)调用一个虚函数时将发生动态绑定**

#### (3)静态类型与动态类型

 当使用存在继承关系的类型时，必须将一个变量或其他表达式的静态类型与该表达式表示对象的动态对象区分开来。表达式的静态类型在**编译时**总是已知的；动态类型则是变量或表达式表示的内存中的对象的类型，往往是在**运行时**才克可知。比如当print_total调用net_price时：

    double ret = item.net_price(n);

  其中item的静态类型是**Quote&**,它的动态类型则依赖于item绑定的实参，动态类型直到运行时调用该函数才会知道。








