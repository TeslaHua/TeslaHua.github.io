---
layout: post
category: "web"
title:  "MarkDown上传图片到Jekyll和Github Pages搭建的博客"
tags: [MarkDown,Jekyll,图片]
---
### 1.MarkDown写博客加载图片，用Jekyll在本地看

我打开Jekyll serve，在Markdown编写博客需要添加图片，我把图片都放在本地博客的Repository目录下的images文件夹下，直接通过URL(http://localhost:4000/images/xxxx.png)在本地http://localhost:4000可以显示图片，用自己的电脑登陆我的博客：www.teslahua.com也可以查看到图片。这是因为我的电脑就作为本地服务器，通过http://localhost:4000端口就可以显示。

<!-- more -->

### 2.在别的电脑访问自己Blog遇到问题

换了一个电脑访问博客，果然图片不显示，此时http://localhost:4000无法调用另一台电脑的资源，于是开始查资料。

### 3.了解到的解决方法

经过查看资料，一般的做法就是寻找第三方的图床平台来放置自己的图片，然后生成图片的链接，直接粘贴在MarkDown的图片URL中即可，评价比较好的是“七牛”平台，注册有10G的免费的流量，但是之后需要收费。

### 4.继续搜索其他方案，设想百度网盘

自己百度网盘还有将近2T的空间，如果能当作图片图床就太好了！

#### （1）首先在自己的百度网盘新建BlogImages文件夹专门用来存放博客需要的图片。>#### （2）上传需要的图片

#### （3）点击图片，并放大查看

#### （4）右键点击放大后的图片，选择“复制图片地址”

#### （5）将该地址粘贴到MarkDown中对应图片的URL处，在上传到Github Pages上面发现本地和博客都可以查看了，成功。

**网上说加载速度有点慢，暂时还没有体现，但是至少是免费**


