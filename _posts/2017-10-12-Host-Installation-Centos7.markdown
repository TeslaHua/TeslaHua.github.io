---
layout: post
category: "Linux"
title:  "真正主机硬盘最小安装CentOS7系统和磁盘分区问题"
tags: [CentOS7,UltraISO,磁盘分区]
---
**提示: 本篇博客主要针对硬盘安装centOS7系统出现的问题**

### 1. 准备工作

   (1). 因为我是在一个硬盘里面只装一个CentOS7系统，所以我选择了全盘格式化，然后删除分区。

   **（记住要删除分区，否则CentOS7自定义分区的时候你的硬盘空闲容量是MB级，因为被占用了）**

   (2). 需要准备以下资源：CentOS7系统镜像、软碟通（UltraISO）、8G优盘

   (3). 先将U盘格式化，然后自行搜索使用UltraISO工具刻录CentOS7镜像的方法。（**在写入硬盘映像选择USB-HDD+写入方式，然后: 便携启动——>写入新的硬盘主引导记录(MBR)——>USB-HDD+——>确定**）

<!-- more -->
### 2. 开始安装系统
    
#### (1). 选择正确的启动项

   进入BIOS，在这里不要选择UEFI BOOT下面的你的U盘那个启动项，**(否则自定义分区的时候会强制要求分一个/boot/efi的分区)**，选择那个USB Starage Device（USB启动），之后进入如下的界面：

   ![open](https://pcs.baidu.com/rest/2.0/pcs/thumbnail?method=generate&app_id=250528&path=%2FBlogImages%2Fopen.jpg&quality=90&size=c10000_u10000)

   选择第一个，然后Enter，接下来一大串Warning，不用管。
   接下来需要确定你的USB启动盘符，分别输入以下命令：

    dracut:/# ls
    dracut:/# cd/dev
    dracut:/dev# ls

   之后会出现下图，然后就可以确定自己的盘符了，这个盘符接下来要用，然后reboot重启：

   ![sdb4](https://pcs.baidu.com/rest/2.0/pcs/thumbnail?method=generate&app_id=250528&path=%2FBlogImages%2Fsdb4.jpg&quality=90&size=c10000_u10000)

   出现界面后，还是选择**Install CentOS7**这个开机启动项，（不过这次不是按Enter键而是选择按键盘上的e键），**（还有一种方法，就是看到右边的大于号，选择它，也是同样的更改自己的盘符，之后按下Enter键进入安装界面）**。前一种方法会出现以下的界面，然后输入自己的USB启动盘符之后同时按下“ Ctrl+x ”，就会进入安装界面了。

   ![panfu](https://pcs.baidu.com/rest/2.0/pcs/thumbnail?method=generate&app_id=250528&path=%2FBlogImages%2Fpanfu.jpg&quality=90&size=c10000_u10000)

   关于安装前的配置，可以继续浏览以下资料：

   <http://blog.csdn.net/u010566813/article/details/40458643>

#### (2). 安装界面的相关操作

   1.选择相应的语言，继续：

   ![language](https://pcs.baidu.com/rest/2.0/pcs/thumbnail?method=generate&app_id=250528&path=%2FBlogImages%2Flanguage.jpg&quality=90&size=c10000_u10000)

   2.如图所示，其中的**软件选择**我选择默认的最小安装，这个安装只提供运行CentOS的基本软件包，最小安装为单一目的的服务器提供需要，并可在这样的安装中最大化性能和安全性。**（主要是我想自己手动安装和配置一些东西）** ，网络配置和主机名这一块我将在下一篇博客里面谈，在这里我们也暂不进行操作，其他的也暂时忽略。

   ![small](https://pcs.baidu.com/rest/2.0/pcs/thumbnail?method=generate&app_id=250528&path=%2FBlogImages%2Fsmall.jpg&quality=90&size=c10000_u10000)

   3.我们直接进行系统分区，选择上图的**安装位置**选项，然后选择自己的硬盘（就是空间很大的那个，如果显示硬盘的空闲空间很小的话就是你的硬盘**没有格式化或没有删除分区**）

   下面是我的关于分区的方案,我们选择LVM逻辑卷（**主要的优势是：灵活性大，可扩展**）（**一般一个‘ / ’分区、一个‘ /usr ’分区、一个‘ /home ’分区、一个‘ /var ’分区、一个‘ /boot ’分区是必要的**）（1T硬盘,8G内存），仅供参考，大神勿喷：

   <table style="border:1px solid Blue">
      <tr>
       <th width="80px">分区名称</th>
       <th width="220px">功能</th>
       <th width="120px">文件系统类型</th>
       <th width="120px">分配容量大小</th>
      </tr>
      <tr>
        <td>/</td>
        <td>根目录，更目录存放系统命令和用户数据等</td>
        <td>xfs</td>
        <td>200GB</td>
      </tr>
      <tr>
        <td>/boot</td>
        <td>bootloader的静态链接文件，存放与Linux启动相关的程序</td>
        <td>xfs</td>
        <td>500MB</td>
      </tr>
      <tr>
        <td>swap</td>
        <td>在物理内存使用完之后,将磁盘空间(也就是SWAP分区)虚拟成内存来使用</td>
        <td>swap</td>
        <td>15G(内存1.5~2倍)</td>
      </tr>
      <tr>
        <td>/usr</td>
        <td>是Linux系统存放软件的地方,如有可能应将最大空间分给它</td>
        <td>xfs</td>
        <td>200GB</td>
      </tr>
      <tr>
        <td>/var</td>
        <td>不断变化的数据，存放服务器的一些服务</td>
        <td>ext4</td>
        <td>100GB</td>
      </tr>
      <tr>
        <td>/temp</td>
        <td>存放临时文件</td>
        <td>xfs</td>
        <td>50GB</td>
      </tr>
      <tr>
        <td>/home</td>
        <td>用户目录，存放普通用户的数据</td>
        <td>xfs</td>
        <td>100GB</td>
      </tr>
      <tr>
        <td>/opt</td>
        <td>附加的应用程序软件包</td>
        <td>xfs</td>
        <td>50GB</td>
      </tr>
      <tr>
        <td>/usr/local</td>
        <td>自已安装程序安装在此</td>
        <td>xfs</td>
        <td>100GB</td>
      </tr>
    </table>

   分区完成了，如下图所示：

   ![fenqu](https://pcs.baidu.com/rest/2.0/pcs/thumbnail?method=generate&app_id=250528&path=%2FBlogImages%2Ffenqu.jpg&quality=90&size=c10000_u10000)

   关于磁盘如何分区的问题，还可以参考以下的资料：

   <https://yq.aliyun.com/articles/68236>
   <http://www.360doc.com/content/15/0715/14/734073_485065070.shtml>

   4.选择安装源，见上上图，我选的是自动检测到安装介质（**虽然我点击验证的时候报错，但是好像并不影响**），然后我就直接选择开始安装。接下来就是进入到安装文件的界面，此时你就可以设置你的用户账号和root账号了，如下图所示,安装完成后你将进入一个全命令行无桌面的linux系统，然后登录就可以了：

   ![root](https://pcs.baidu.com/rest/2.0/pcs/thumbnail?method=generate&app_id=250528&path=%2FBlogImages%2Froot.jpg&quality=90&size=c10000_u10000)






   

 














