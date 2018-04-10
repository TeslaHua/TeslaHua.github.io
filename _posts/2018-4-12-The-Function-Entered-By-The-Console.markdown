---
layout: post
category: "slam"
title:  "C/C++的从控制台获取输入的方法"
tags: [C++,输入,控制台]
---

##C/C++从控制台获取输入的几种方法

### 1.C语言风格的控制台输入方法

（1）格式化输入函数scanf

	scanf(“%d, %d”, &i, &j);

   上例中的scanf()函数先读一个整型数, 然后把接着输入的逗号剔除掉, 最后读入另一个整型数。如果“,”这一特定字符没有找到, scanf()函数就终止。若参数之间的分隔符为空格, 则参数之间必须输入一个或多个空格。

<!-- more -->

   当使用多个scanf()函数连续给多个字符变量输入时, 例如:

	char c1, c2;
	scanf（“%c”, &c1);
	scanf(“%c”, &c2);
	printf(“c1 is %c, c2 is %c”, c2, c2);

   运行该程序, 输入一个字符A后回车 (要完成输入必须回车), 在执行scanf(“%c”, &c1)时, 给变量c1赋值”A”, 但回车符仍然留在缓冲区内, 执行输入语句scanf(”%c”, &c2)时, 变量c2输出的是一空行, 如果输入AB后回车, 那么输出结果为: c1 is A, c2 is B。要解决以上问题, 可以在输入函数前加入清除函数fflush()。修改以上程序变成:

	char c1, c2;
	scanf(“%c”, &c1);
	fflush(stdin);
	scanf(“%c”, &c2);
	printf(“c1 is %c, c2 is %c”, c1, c2);

（2）非格式化输入输出函数

   非格式化输入输出函数可以由上面讲述的标准格式化输入输出函数代替, 但这些函数编译后代码少, 相对占用内存也小, 从而提高了速度, 同时使用也比较方便。下面分别进行介绍。

   a). puts()函数用来向标准输出设备(屏幕)写字符串并换行, 其调用格式为:puts(s);其中s为字符串变量(字符串数组名或字符串指针)。puts()函数的作用与语printf(”%s\n”, s)相同。

**说明:**

   (1). puts()函数只能输出字符串, 不能输出数值或进行格式变换。

   (2). 可以将字符串直接写入puts()函数中。如:puts(“Hello, Turbo C2.0”);

   b). gets()函数用来从标准输入设备(键盘)读取字符串直到回车结束, 但回车符不属于这个字符串。其调用格式为:gets(s);其中s为字符串变量(字符串数组名或字符串指针)。gets(s)函数与scanf(“%s”, &s)相似, 但不完全相同, 使用scanf(“%s”,&s)函数输入字符串时存在一个问题, 就是如果输入了空格会认为输入字符串结束,空格后的字符将作为下一个输入项处理, 但gets() 函数将接收输入的整个字符串直到回车为止。
 
**说明:**

   (1). gets(s)函数中的变量s为一字符串。如果为单个字符, 编译连接不会有错误, 但运行后会出现"Null pointer asignmemt"的错误。

   c). putchar()函数是向标准输出设备输出一个字符, 其调用格式为:putchar(ch);其中ch为一个字符变量或常量。putchar()函数的作用等同于printf("%c", ch);

   d).  getch()和getche()函数  这两个函数都是从键盘上读入一个字符。其调用格式为:getch(); getche();两者的区别是:getch()函数不将读入的字符回显在显示屏幕上, 而getche()函数却将读入的字符回显到显示屏幕上。

   e). getchar()函数也是从键盘上读入一个字符, 并带回显。它与前面两个函数的区别在于: getchar()函数等待输入直到按回车才结束, 回车前的所有输入字符都会逐个显示在屏幕上。但只有第一个字符作为函数的返回值。getchar()函数的调用格式为:getchar();


### 1.C++语言风格的控制台输入方法

（1） >>操作符

   C++中的cin是一个 istream对象，从标准输入中读取数据，在iostream头文件中定义。cin从缓冲区读取数据，有多种方式，如操作符 >>  函数getline()、get()等.

   ** >> ** 根据后面的变量的类型读取数据; **读取时结束条件：ENTER, SPACE, TAB; 对结束符的处理：丢掉.**,并且一开始读取到空格符会忽略直到读取到符合后面的变量的类型的内容。例如：

	string str;
	std::cin>>str;

   如果输入是“      hello   world”，str中存入的字符串也只是“hello”.

   (2) getline()函数

   C++中本质上有两种getline函数，一种在头文件<istream>中，是istream类的成员函数。一种在头文件<string>中，是普通函数。

   在<istream>中的getline函数有两种重载形式：

	istream& getline (char* s, streamsize n );
	istream& getline (char* s, streamsize n, char delim );

   作用是从istream中读取至多n个字符保存在s对应的数组中。即使还没读够n个字符，如果遇到换行符'\n'（第一种形式）或delim（第二种形式），则读取终止，'\n'或delim都不会被保存进s对应的数组中。 示例（摘自cplusplus.com）：

	// istream::getline example
	#include <iostream>     // std::cin, std::cout	
	int main () {
	char name[256], title[256]; 
	std::cout << "Please, enter your name: ";
	std::cin.getline (name,256); 
	std::cout << "Please, enter your favourite movie: ";
	std::cin.getline (title,256);
	std::cout << name << "'s favourite movie is " << title;
	return 0;

   在<string>中的getline函数有四种重载形式：

	istream& getline (istream&  is, string& str, char delim);
	istream& getline (istream&& is, string& str, char delim);
	istream& getline (istream&  is, string& str);
	istream& getline (istream&& is, string& str);

   用法和cin类中的getline()类似，不过要读取的istream是作为参数is传进函数的。读取的字符串保存在string类型的str中。示例（摘自cplusplus.com）：

	// extract to string
	#include <iostream>
	#include <string>
	 
	int main ()
	{
	  std::string name;
	 
	  std::cout << "Please, enter your full name: ";
	  std::getline (std::cin,name);
	  std::cout << "Hello, " << name << "!\n";
	 
	  return 0;
	}

  **说明：**
	getline()其实把换行符也读进去了，只不过不把换行符保存到那个string对象中。同理，结束符delim也不会被写入到string对象中。getline()会读取换行符之前的所有的空白符，包括开头的空白符，如：

   如果输入是“    hello    world    ”，str中存入的字符串也只是“    hello    world   ”.



