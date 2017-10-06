---
layout: post
category: "web"
title:  "Jekyll搭建的博客首页设置文章摘要"
tags: [ReadMore,Jekyll,excerpt]
---
### 1.按照Jekyll官方ReadMore方案来设置

基本功能就是博客首页只出现文章的摘要，点击ReadMore,可以阅读全文：

（1）首先打开_config.yml文件，添加代码：excerpt_separator: "<!-- more -->";
    
（2）然后打开自己的index.html文件，添加代码：{{ post.excerpt }}，代码段如下：

	<hr>
	{{ post.excerpt }}   
	<p><h3>
	<a href="{{ post.url }}">ReadMore &raquo;</a>
	</h3></p>
	<hr>

<!-- more -->
 (3)然后在你的markdown博客里面在想要摘要截止的地方添加<!-- more -->这句话，就像：

     ---
	 layout: post
     category: "read"
	 title:  "《沁园春·雪》"
	 tags: [阅读,人生]
	 ---
	 北国风光，千里冰封，万里雪飘。望长城内外，惟馀莽莽；大河上下，顿失滔滔。
	 山舞银蛇，原驰蜡象，欲与天公试比高。须晴日，看红妆素裹，分外妖娆。
	
	 <!-- more -->  <！--摘要截止到这-->
	 江山如此多娇，引无数英雄竞折腰。惜秦皇汉武，略输文采；唐宗宋祖，稍逊风骚。
	 一代天骄，成吉思汗，只识弯弓射大雕。俱往矣，数风流人物，还看今朝。

### 2.另一种方法来实现ReadMore功能

（1）打开自己的index.html文件，添加代码：{{ post.content ||split:'<!-- more -->'| first }},添加位置和官方位置一样。

（2）然后在你的markdown博客里面在想要摘要截止的地方添加<!-- more -->这句话，如上。

（3）这种方法有一个缺点就是，在用jekyll serve本地更新时，会出现一个警告：

    Liquid Warning: Liquid syntax error (line 13): Expected id but found pipe
	in "{{ post.content ||split:'<!-- more -->' | first }}" in index.html








