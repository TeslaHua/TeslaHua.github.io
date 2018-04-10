---
layout: post
category: "slam"
title:  "C++的友元函数再探"
tags: [C++,友元函数]
---

**主要利用一个程序来解释C++的友元函数**
### 1.简述程序的要求

1.类x有私有成员i;
2.类y的成员函数g(x *)是类x的友元函数，实现对类x的成员i加1;
3.类z是类x的友元类，其成员函数f(x*)实现对类x的成员i加5；
4.函数h(x*)是类x的友元函数，实现对类x的成员i加10。

<!-- more -->

### 2.代码如下

	#include <iostream>
	using namespace std; 
	//这里必须先声明X类，因为Y类的定义中的g(X &x)用到了X，Z类的定义中的f(X &x)用到了X
	class X;  
	//Y的类定义必须在类X之前，并且成员函数g(X &x)不能被定义，只能被声明
	class Y{      
		public: Y() = default;
		public: void g(X &x);   //这里只能声明，因为还不知道Y的函数g是X的友元函数
	};
	//Z的类至少要在X之前被声明，在这里我们直接把Z类定义了
	class Z{   
		public: Z() = default;
		public: void f(X &x);   //这里只能声明，因为还不知道Z是X的友元类
	};
	class X{
		friend void Y::g(X &);   //在这里再声明一下，也不需要定义，X中的i加5
		friend class Z;          //指定Z是X的友元类,并让Z的成员函数f把X中的i加5
		friend void h(X &);      //h(x*)是类x的友元函数，实现对类x的成员i加10。

		private: double i = 0;
		public: X() = default;
		public: void setI(double i_temp){    //修改器  
			 this->i = i_temp;
		 }
		public: double getI(){               //访问器
			 return this->i;
		 }		 
	};
	//实现Y作用域下的友元g(X &x)
	void Y::g(X& x){
		std::cout << "没有增加之前的i是：" << x.i << std::endl;
		++x.i;
		std::cout << "增加后i的值是：" << x.i << std::endl;
	}
	//实现友元类Z作用域下的友元f(X &x)
	void Z::f(X& x){
		std::cout << "没有增加之前的i是：" << x.i << std::endl;
		x.i = x.i + 5;
		std::cout << "增加后i的值是：" << x.i << std::endl;
	}
	//实现友元函数h(X &x)
	void h(X& x){
		std::cout << "没有增加之前的i是：" << x.i << std::endl;
		x.i = x.i + 10;
		std::cout << "增加后i的值是：" << x.i << std::endl;
	}

	int main(int argc, char * argv[]){
		X x;
		Y y;
		Z z;
		y.g(x);
		z.f(x);
		h(x);     //到这里X中的变量i已经变成了16
		return 0;
	}






   
 

  




