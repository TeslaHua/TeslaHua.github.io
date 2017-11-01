---
layout: post
category: "slam"
title:  "C++继承中类的作用域问题"
tags: [C++,继承,作用域]
---

### 1.在编译时进行名字查找

    class Disc_quote: public Quote{
	public:
		std::pair<size_t,double>discount_policy() const
			{return xxxx;};
		//其他成员与之前的版本一致
	};

<!-- more -->

  我们只能通过Disc_quote及其派生类的对象、引用或者指针使用discount_policy：

	Bulk_quote bulk;
	Bulk_quote *bilkP = &bulk; //静态类型与动态类型一致
	Quote *itemP = &bulk;      //静态类型与动态类型不一致
	bulkP->discount_policy();  //正确：bulkP的类型是Bulk_quote*
	itemP->discount_policy();  //错误：itemP的类型是Quote*

  尽管在bulk中确实有一个名为discount_policy的成员，但是该成员对于itemP确实不可见的。itemP的指针是Quote的指针，意味着对discount_policy的搜索将从Quote开始。又因为Quote不包含名为discount_policy的成员，所以我们无法通过Quote的对象、引用或者指针来调用discount_policy。

  ![jicehng](http://oyqo0q1a2.bkt.clouddn.com/jicheng.png)

### 2.名字查找先于类型检查

    struct Base {
		int memfun();
	};
	struct Derived : Base {
		int memfun(int);  //隐藏基类的memfun
	};
	Derived d;Base b;
	b.memfun();        //调用Base::memfun
	d.memfun(10);      //调用Derived::memfun
	d.memfun();        //错误：参数列表为空的memfun被隐藏
	d.Base::memfun();  //正确:调用Base::memfun

### 3.虚函数与作用域

    class Base {
	public:
		virtual int fun();
	};

	class D1 :public Base{
	public:
		//隐藏基类的fun,这个fun不是虚函数
		//D1继承了Base::fun()的定义
		int fun(int);    //形参列表与Base中的fun不一致
		virtual void f2();   //是一个新的虚函数，在Base中不存在
	}

	class D2 : public D1 {
	public:
		int fun(int);    //是一个非虚函数，隐藏了D1::fun(int)
		int fun();       //覆盖了Base的虚函数fun
		void f2();       //覆盖了D1的虚函数f2
	};

### 4.通过基类调用隐藏的虚函数

	 Base bobj; D1 d1obj; D2 d2obj;

	 Base * bp1 = &bobj, * bp2 = &d1obj, *bp3 = &d2obj;
	
	 bp1->fun();     //虚调用，将在运行时调用Base::fun
	 bp2->fun();     //虚调用，将在运行时调用Base::fun
	 bp3->fun();     //虚调用，将在运行时调用D2::fun
	
	 D1 *d1p = &d1obj; D2 *d2p = &d2obj;

	 bp2->f2();       //错误：Base没有名为f2的成员    
	 d1p->f2();       //虚调用，将在运行时调用D1::f2()
	 d2p->f2();       //虚调用，将运行时调用D2::f2()
  





