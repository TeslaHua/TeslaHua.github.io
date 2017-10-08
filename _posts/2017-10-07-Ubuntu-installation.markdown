---
layout: post
category: "Linux"
title:  "ubuntu16.04开机启动错误、服务管理程序以及显卡安装问题"
tags: [ubuntu,NVIDIA,服务程序]
---
### 1.在虚拟机上安装完ubuntu之后的启动问题,全部的问题如下图：

   ![allerror](https://pcs.baidu.com/rest/2.0/pcs/thumbnail?method=generate&app_id=250528&path=%2FBlogImages%2Fallerror.png&quality=90&size=c10000_u10000)

<!-- more -->
#### （1）问题1：Assuming drive cache:write through
   
   这个提示当时在安装ubuntu的时候一闪而过，之后安装完重启的时候就卡在那里很久，之后每次启动都有这个提示，google了很久也没有找到解决的办法，不过网上有说U盘的问题，也有说是ubuntu16.04LTS这个版本的Bug.具体的相关解决办法请参考以下链接：
   <http://blog.csdn.net/xinxing__8185/article/details/43700333>
   
#### （2）问题2：/dev/sda1:recovering journal

   这个问题google之后，网上说这不是一个Error,它是一个强制的文件系统检查, 以确保虚拟磁盘的完整性，这样看起来倒像是个好事。

   原文： It's not a "problem" at all, it's a forced file system check to make sure of the integrity of the virtual disk. It does not hurt a thing, in fact, it's a good thing.

#### （3）问题3：/dev/sda1：Clearing orphaned inode <some inode number>

   下面出来的这几行信息可能就是和recovering journal有关而打印出来的日志信息了，但是我直接重启的话就不会出现recovering journal和它下面的这些输出信息，所以怀疑是开机的引导程序或者硬件检测这里除了问题。
#### （4）问题4：/dev/sda1: clean, 220240/30269440 file, 2971359/121076736 blocks

   这好像也不是一个Error，只是自检到你的硬盘驱动器没有损坏-这是Ubuntu在检查每个启动项。

#### （5）问题5：Host SMBus controller not enabled

   这个貌似是SMbus总线和ic2冲突的问题，解决方法是：

   1.将该模块列入不装入名单，编辑文件：

     sudo vi /etc/modprobe.d/blacklist.conf

   2.在文件末尾加入：
     
     blacklist i2c_piix4

   3.**:wq保存退出，reboot看效果。**
#### （6）问题6：由于在vi编辑blacklist.conf文件时错输入了一些东西，导致重启出错如下提示：

   ![libkmod](https://pcs.baidu.com/rest/2.0/pcs/thumbnail?method=generate&app_id=250528&path=%2FBlogImages%2Flibkmod.png&quality=90&size=c10000_u10000)

   最难过的是当你发现即使你删除了误输入的东西，你启动仍然还是有一样的提示，经过查询，涉及到init ram filesystem（它是一个cpio格式的内存文件系统），就是当你修改了文件之后，并不能马上生效，需要更新一下这个文件系统，在终端输入如下指令：
   
    update-initramfs -u

   然后发现错误提示没有了。

### 2.有关ubuntu的服务管理程序sysv-rc-conf

#### （1）linux系统运行的等级

   Linux 系统任何时候都运行在一个指定的运行级上，并且不同的运行级的程序和服务都不同，所要完成的工作和要达到的目的都不同，系统可以在这些运行级之间进行切换，以完成不同的工作。

   运行级别（Runlevel）指的是Unix或者Linux等类Unix操作系统下不同的运行模式。

   大多数linux操作系统下一共有如下6个典型的运行级别：

    0 停机
    1 单用户，Does not configure network interfaces, start daemons, or allow non-root logins
    2 多用户，无网络连接 Does not configure network interfaces or start daemons
    3 多用户，启动网络连接 Starts the system normally.
    4 用户自定义
    5 多用户带图形界面
    6 重启

    查看当前系统的运行级别可以使用命令：runlevel

    切换运行级别：init [0123456Ss]  

#### （2）Linux主要的启动步骤：

    1. 读取 MBR 的信息,启动 Boot Manager
       Windows 使用 NTLDR 作为 Boot Manager,如果您的系统中安装多个版本的 Windows,您就需要在 NTLDR 中选择您要进入的系统。Linux 通常使用功能强大,配置灵活的 GRUB 作为 Boot Manager。

    2. 加载系统内核,启动 init 进程
       init 进程是 Linux 的根进程,所有的系统进程都是它的子进程。

    3. init 进程读取 /etc/inittab 文件中的信息,并进入预设的运行级别,
       按顺序运行该运行级别对应文件夹下的脚本。脚本通常以 start 参数启动,并指向一个系统中的程序。
       通常情况下, /etc/rcS.d/ 目录下的启动脚本首先被执行,然后是/etc/rcN.d/ 目录。例如您设定的运行级别为 3,那么它对应的启动目录为 /etc/rc3.d/ 。

    4. 根据 /etc/rcS.d/ 文件夹中对应的脚本启动 Xwindow 服务器 xorg
       Xwindow 为 Linux 下的图形用户界面系统。

    5. 启动登录管理器,等待用户登录
       Ubuntu 系统默认使用 GDM 作为登录管理器,您在登录管理器界面中输入用户名和密码后,便可以登录系统。(您可以在 /etc/rc3.d/文件夹中找到一个名为 S13gdm 的链接)

#### （3）在ubuntu下面安装sysv-rc-conf

     在Ubuntu下用apt-get安装： sudo apt-get install sysv-rc-conf

     运行： sudo sysv-rc-conf
    
   效果如下图，其中“ X ”符号代表开机会启动的服务，点击X号就会取消该服务，然后按“ q ”键退出。

   ![sysv-rc-conf](https://pcs.baidu.com/rest/2.0/pcs/thumbnail?method=generate&app_id=250528&path=%2FBlogImages%2Fsysv-rc-conf.png&quality=90&size=c10000_u10000)

#### （4）常见的系统服务信息

    acpi-support 高级电源管理支持
	acpid acpi 守护程序.这两个用于电源管理,非常重要
	alsa 声音子系统
	alsa-utils
	anacron cron 的子系统,将系统关闭期间的计划任务,在下一次系统运行时执行。
	apmd acpi 的扩展
	atd 类似于 cron 的任务调度系统。建议关闭
	binfmt-support 核心支持其他二进制的文件格式。建议开启
	bluez-utiles 蓝牙设备支持
	bootlogd 启动日志。开启它
	cron 任务调度系统,建议开启
	cupsys 打印机子系统。
	dbus 消息总线系统(message bus system)。非常重要
	dns-clean 使用拨号连接时,清除 dns 信息。
	evms 企业卷管理系统(Enterprise Volumn Management system)
	fetchmail 邮件用户代理守护进程,用于收取邮件
	gdm gnome 登录和桌面管理器。
	gdomap
	gpm 终端中的鼠标支持。
	halt 别动它。
	hdparm 调整硬盘的脚本,配置文件为 /etc/hdparm.conf。
	hibernate 系统休眠
	hotkey-setup 笔记本功能键支持。支持类型包括: HP, Acer, ASUS, Sony,Dell, 和 IBM。
	hotplug and hotplug-net 即插即用支持,比较复杂,建议不要动它。
	hplip HP 打印机和图形子系统
	ifrename 网络接口重命名脚本。如果您有十块网卡,您应该开启它
	inetd 在文件 /etc/inetd.conf 中,注释掉所有你不需要的服务。如果该文件不包含任何服务,那关闭它是很安全的。
	klogd 重要。
	linux-restricted-modules-common 受限模块支持。
	/lib/linux-restricted-modules/ 文件夹中的模块为受限模块。例如某些驱动程序,如果您没有使用受限模块,就不需要开启它。
	lvm 逻辑卷管理系统支持。
	makedev 创建设备文件,非常重要。
	mdamd 磁盘阵列
	module-init-tools 从/etc/modules 加载扩展模块,建议开启。
	networking 网络支持。按 /etc/network/interfaces 文件预设激活网络,非常重要。
	ntpdate 时间同步服务,建议关闭。
	pcmcia pcmcia 设备支持。
	powernowd 移动 CPU 节能支持
	ppp and ppp-dns 拨号连接
	readahead 预加载库文件。
	reboot 别动它。
	resolvconf 自动配置 DNS
	rmnologin 清除 nologin
	rsync rsync 守护程序
	sendsigs 在重启和关机期间发送信号
	single 激活单用户模式
	ssh ssh 守护程序。建议开启
	stop-bootlogd 在 2,3,4,5 运行级别中停止 bootlogd 服务
	sudo 检查 sudo 状态。重要
	sysklogd 系统日志
	udev & udev-mab 用户空间 dev 文件系统(userspace dev filesystem)。重要
	umountfs 卸载文件系统
	urandom 随机数生成器
	usplash 开机画面支持
	vbesave 显卡 BIOS 配置工具。保存显卡的状态
	xorg-common 设置 X 服务 ICE socket。
	adjtimex 调整核心时钟的工具
	dirmngr 证书列表管理工具,和 gnupg 一起工作。
	hwtools irqs 优化工具
	libpam-devperm 系统崩溃之后,用于修理设备文件许可的守护程序。
	lm-sensors 板载传感器支持
	mdadm-raid 磁盘陈列管理器
	screen-cleanup 清除开机屏幕的脚本
	xinetd 管理其他守护进程的一个 inetd 超级守护程序

### 3.关于ubuntu安装NVIDIA显卡驱动

#### （1）我是看网上有人通过安装显卡驱动解决了上述Assuming drive cache:write through的问题，所以就尝试安装，按照以下步骤安装：

    #安装一个依赖文件，并更新系统
	sudo apt-get install linux-headers-generic
	sudo apt-get update
	sudo apt-get upgrade
	#查询NVIDIA显卡驱动，并安装
	sudo apt-cache search nvidia*
	#选择一个你需要的，如nvidia 367 375
	sudo apt-get install nvidia-367
	sudo nvidia-xconfig
	sudo reboot

#### （2）安装完之后发现登录之后又会重新回到登录的界面，进不去桌面。

   网上有人说是因为：我们安装的ubuntu的显示器并没有接到nvidia的显卡上，而是使用了intel的集显。我们安装驱动其实只是想将我们运算的显卡的驱动更新，结果都给搞了，所以产生了冲突。当然，也可能时opengl产生的冲突。所以接下来，开始卸载：

     1.同时按下Ctrl+Alt+F1 （F1~F6其中一个就可以）

     2.然后输入用户名，回车，输入密码，回车，成功进入到shell，开始卸载NVIDIA驱动
   
     3.sudo apt-get remove --purge nvidia-*
	   sudo apt-get install ubuntu-desktop
	   sudo rm /etc/X11/xorg.conf
	   echo 'nouveau' | sudo tee -a /etc/modules
	   #重启系统
	   sudo reboot



    













