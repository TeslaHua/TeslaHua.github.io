---
layout: post
category: "web"
title:  "Chrome下Ajax跨域问题"
tags: [Ajax,跨域,Chrome]
---
>## 1.在Chrome浏览器下通过Ajax异步请求访问本地文件 ##

>总是卡在请求文件那里，于是就在Chorme下简单调试了一下，发现一触发send button,就报错：
>Cross origin requests are only supported for protocol schemes: http, data, >chrome, chrome-extension, https. 如下图所示：

>![Error](http://localhost:4000/images/error.png)

>百度了一下：项目开发过程中，用到了Ajax异步请求。若将项目放在本地服务器中，通过localhost访
>问文件，不会报错。若直接通过file://访问文件就会报错。 
>这是因为直接通过file://访问文件，Ajax本地跨域。浏览器为了安全性考虑，默认对跨域访问禁止。

>## 2.解决方法
>给浏览器传入启动参数（--allow-file-access-from-files），允许跨域访问.
>右键单击Chrome浏览器快捷方式，选择属性，修改目标路径，在后面加上：--allow-file-access->from-files,注意**--allow前面有一个空格哦**，不然操作会提示无效。如下图：


>![ChromeConfig](http://localhost:4000/images/ChromeConfig.png)


>#### **这里注意两点：1.--allow前面有空格；2.如果设置的是桌面的快捷方式，那么任务栏的不可用**

>## 3.重新打开浏览器，加载网页文件
>发现Ajax可以正常异步请求本地文件了，如下图：

>![right](http://localhost:4000/images/right.png)

>#### 关于Chrome浏览器下Ajax异步请求跨域的问题相关链接：
><http://blog.csdn.net/rywaqpf/article/details/44836965>

