---
layout: post
category: "slam"
title:  "C++中类中的友元函数、友元类以及内联函数"
tags: [C++,class,声明，定义]
---

### 1.类中的友元关系

#### (1)类中的友元函数

  类可以允许其他类或者函数访问它的非公有成员，方法是令其他类或者函数成为它的**友元**。需要加一条以friend关键字开头的函数声明语句即可。例如：

<!-- more -->

     struct Salas_data{
		//声明为该类的友元函数之后，就可以访问该类的非公有成员

        friend Salas_data add(const Salas_data&,const Salas_data&);
		friend std::ostream &print(std::ostream&,const Salas_data&);
        friend std::istream &read(std::istream&,Salas_data&);

		//新成员：关于Salas_data对象的操作
		public:
        std::string isbn() const{ return bookNo;}
        Salas_data& combine(const Salas_data&);
		double avg_price() const;
		//数据成员
		private:
		std::string bookNo;
		unsigned units_sold = 0;
		double revenue = 0.0;
     };

       //Salas_data的非成员接口函数
       //虽然上述在类的内部进行了友元的声明，但是这里的声明也是必须的。
	   Salas_data add(const Salas_data&,const Salas_data&);
       std::ostream &print(std::ostream&,const Salas_data&);
       std::istream &read(std::istream&,Salas_data&);

**友元声明只能出现在类定义的内部，但是在类内出现的位置没有要求，最好集中声明友元。**
  
#### (2)类之间的友元关系

  假如我们需要为Window_mgr添加一个名为clear的成员，它负责把一个指定的Screen的内容设为空白。clear需要访问Screen的私有成员；而要想令这种访问合法，Screen需要把Window_mgr指定为它的友元：

     class Screen{
		//Window_mgr的成员可以访问Screen类的所有成员
        friend class Window_mgr;  //指定为友元类
	 }

**如果一个类指定了友元类，则友元类的成员函数可以访问此类包括非公有成员在内的所有成员**，但是友元关系不存在传递性。如果Window_mgr有它自己的友元，则这些友元不具有访问Screen的特权。

#### (3)把别的类中的函数设为友元函数

  除了令整个Window_mgr作为友元之外，Screen还可以只为Window_mgr作为友元之外，Screen还可以只为Window_mgr中的clear函数提供访问权限：

    class Screen{
	   friend void Window_mgr::clear(.....);
	} 

    **必须指出该成员函数属于哪个类，如上述的Window_mgr**

  若想把别的类中的函数设为友元函数，我们必须按照如下的设计步骤：

  1. 首先定义Window_mgr类，其中声明clear函数，**但是不能定义它**;
  2. 在clear使用Screen的成员之前必须先声明Screen;
  3. 接下来定义Screen,包括对于clear的友元声明；
  4. 最后定义clear,此时它才可以使用Screen的成员。

### 2.类中的成员设置为内联函数

  在类中常有一些规模较小的函数适合被声明为内联函数。其中定义在类内部的成员函数是自动inline的。例如：

	class Screen{
	    public:
        Screen(int ht,int wd): height(ht),width(wd);  //有参构造函数
  		char get() const {  //隐式内联
			return cursor;
		}
		inline char get(int height,int width) const; //显式内联
		Screen &move(int height,int width);   //此后在设为内联函数

        private:
		   int height = 0,width = 0;
	}

  我们可以在类的内部把inline作为声明的一部分显式的声明成员函数，同样的，也能在类的外部用inline关键字修饰函数的定义。

    inline Screen &Screen::move(int height,int width){
	  height = height + 10;
	  width = width + 10;
      return *this;    //以左值的形式返回对象
	}

### 3.重载成员函数

    Screen myscreen；
	char ch = myscreen.get();  //调用Screen::get()  
    ch = myscreen.get(0,0);    //调用Screen::get(int height ,int width)









