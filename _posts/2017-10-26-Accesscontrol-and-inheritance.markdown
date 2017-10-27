---
layout: post
category: "slam"
title:  "C++中类成员的访问控制与继承"
tags: [C++,public,private,protected]
---

### 1.受保护的成员

 一个protected关键字来声明那些它希望与派生分享但是不想被其它公共访问的成员。protected说明符可以看作是public和private中和后的产物。

  1.和私有成员类似，受保护的成员对于类的用户来说不可访问
  2.和公有成员类似，受保护的成员对于派生类的成员和友元来说是可访问的
  3.派生类的成员或友元之只能通过**派生类对象**来访问基类的受保护成员，**派生类对于一个基类对象中的受保护的成员没有任何访问特权**

    class Base {
	  protected:
  		int prot_mem;  
	};
	class Sneaky : public Base {
	   //能访问Sneaky::prot_mem	
	   friend void clobber(Sneaky&);
	   //不能访问Base::prot_mem
	   friend void clobber(Base&);
	   int j;    //默认是private  
	};
    //正确：clobber能访问Sneaky对象的private和protected成员
	void clobber(Sneaky &s) { s.j = s.prot_mem = 0;}
    //错误：clobber不能访问Base的protected成员
	void clobber(Base &b) { b.prot_mem = 0;}

### 2.公有、私有和受保护继承

  某个类对其继承而来的成员的访问权限受到两个因素影响: 一是在基类中该成员的访问说明符；二是在派生类的派生列表中的访问说明符。

    class Base {
	public:
    	void pub_mem();
	protected:
		int prot_mem;
	private:
		char priv_mem;
	};

    struct Pub_Derv: public Base{
		//正确：派生类能访问protected成员
		int f() { return prot_mem;}
        //错误：private成员对于派生类来说不可见
        char g() { return priv_mem;}
	}; 

    struct Priv_Derv : private Base { 
		//private不影响派生类的访问权限
		int f1() const { return prot_mem;}
	} 

  派生访问说明符对于派生类的成员(及友元)能否访问其直接基类的成员没什么影响。对于基类成员的访问权限只与基类的访问说明符有关。Pub_Derv和Priv_Derv都能访问受保护的成员prot_men,同时他们都不能访问私有成员priv_mem.

  **派生访问说明符的目的是控制派生类用户(包括派生类的派生类在内)对于基类成员的访问权限：**

    Pub_Derv d1;  //继承自Base的成员是public的
	Priv_Derv d2;  //继承自Base的成员是private的
	d1.pub_mem();  //正确：pub_mem在派生类中是public的
	d2.pub_men();  //错误：pub_mem在派生类中是private

  Pub_Derv和Priv_Derv都继承了pub_mem函数，如果继承是**公有(public)的**或者是**受保护的(protected)的**，则成员将遵循其原有的访问说明符，此时d1可以调用pub_mem；在Priv_Derv中，Base的成员是私有的，因此类的用户不能调用pub_mem。

### 3.友元与继承

  就像友元关系不能传递一样，友元关系同样也不能继承。基类的友元在访问派生类时不具有特殊性，类似的，派生类的友元也不能随意访问基类的成员：

    class Base{
		//添加friend声明，其他成员与之前一致
		friend class Pal;    
	}
    class Pal {
		public:
		    int f(Base b) {return b.prot_mem;} //正确：Pal是Base的友元
			int f2(Sneaky s) {return s.j;}  //错误：Pal不是Sneaky的友元
        //对基类的访问权限由基类的本身控制，即使对于派生类的基类部分也是如此
            int f3(Sneaky s) { return s.prot_mem;} //正确：Pal是Base的友元   
	}

 Pal是Base的友元，所以Pal能够访问Base对象的成员，这种可能性包括了Base对象内嵌在其派生类对象的情况。当一个类将另一个类声明为友元时，这种关系只对做出声明的类有效。对于原来的那个类来说，其友元的基类或者派生类不具有特殊的访问能力：

    //D2对Base的protected和private成员不具有特殊的访问能力
    class D2 : public Pal {
	public:
		int mem(Base b){
			return b.prot_mem;    //错误：友元关系不能继承
		}
	}

### 4.默认的继承保护级别

    class Base {/*....*/};
	struct D1: Base {/*.....*/};  //默认public继承
	class D2: Base {/*....*/};    //默认private继承






