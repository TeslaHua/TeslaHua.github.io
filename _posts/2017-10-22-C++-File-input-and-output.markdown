---
layout: post
category: "slam"
title:  "C++中的简单文件的输入与输出"
tags: [C++,file,input,output]
---

### 1. 写入到文本文件中

   1.声明ofstream对象

     ofstream outFile;  //outFile an ofstream object
     ofstream fout;    //fout an ofstream object

<!-- more -->

   2.将对象与特定文件关联
     
     方法1：
     outFile.open("fish.txt"); //写入的目标文件
     方法2：
     char filename[50];
     cin >>filename;
     fout.open(filename);

     open()接受一个字符串为参数，也接受一个存储在数组里面的字符串

   3.如何使用

    char array[20] = {" hello C++!"};  //一个需要写入的字符串

	ofstream outFile;           //声明一个输入文件对象
	outFile.open("hello.txt");  //指定写入的目标文件
	outFile<<array<<endl;       //将array数组的内容写入hello.txt文件
	outFile.close();            //关闭输入流

	ofstream fout;              //声明一个输入文件对象
	char filename[20];          //定义一个数组，存放输入的文件名
	cin>>filename;              //等待输入的写入的目标文件名
	fout.open(filename);        //指定写入的目标文件
	fout<<array<<endl;          //将array数组的内容写入filename文件
	fout.close();               //关闭输入流

**如果目标文件已经存在，默认清空原来文件内容，然后再写入新内容**

### 2.读取文本文件

   1.声明ofstream对象
   
    ifstream inFile;
    ifstream fin;

   2.将对象与特定文件关联

    inFile.open("hello.txt");
    char filename[50];
    cin >> filename;
    fin.open(filename);

   3.如何使用

	char arrays[20]; //定义一个读取的数据存放的位置
	ifstream inFile; //声明对象

	inFile.open("hello.txt");

    //验证是否能打开该文件，比如文件不存在就打不开
	if(!inFile.is_open()){
		cout<<"Could not open the file "<<endl;
		cout<<"program terminating.\n";
		exit(EXIT_FAILURE);
	}

	//inFile>>arrays;  这个只能读取到空格之前的字符串 
	//cout<<arrays<<endl;
	inFile.getline(arrays,20);  //读取文本文件的一行
	cout<<arrays<<endl;
	inFile.close();  //关闭输出流

	ifstream fin;
    char filename[50];
    cin >> filename;
    fin.open(filename);
	if(!fin.is_open()){
		cout<<"Could not open the file "<<endl;
		cout<<"program terminating.\n";
		exit(EXIT_FAILURE);
	}
	//fin>>arrays;
	//cout<<arrays<<endl;
	fin.getline(arrays,20);
	cout<<arrays<<endl;
	fin.close();
	return 0;









    




  

   



   
 


 














