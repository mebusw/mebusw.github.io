title: 在Raid5磁盘阵列上安装Ubuntu11
date: 2011-06-13 22:31:45
tags:
	- raid
	- ubuntu
categories:
    - programming
    - tools
---

新配服务器，intel i3双核，微星主板，8G内存，三块500GB硬盘组成1TB的Raid5阵列，因为其中一块是备份磁盘。raid5的好处是既能使磁盘读写带宽加倍，又具有热备份功能，还比raid0+1少用一块硬盘。

 
因为是8G内存，所以下载了Ubuntu 11.04桌面版。从liveCD引导，一路回车，到磁盘分区时，提示无法在分区上安装启动程序，列表中可以选择的有/dev/sda，raid-p0等，无论选哪个都不行。

<!--more-->
 
重启，直接进去liveCD，发现raid是顺利识别出的。再次安装，这次到磁盘分区时，可供安装启动程序的列表中，多了/dev/dm-0，这才是raid磁盘驱动支持，但是似乎不稳定，时有时无，而且安装完了无法启动。
 
回到BIOS，把raid控制器禁掉，这次连liveCD都进不去，不断提示无法找到/dev/sda2等，只得又改回来。
 
重新下Ubuntu 11 Server版，到了分区时候，分区表变成了只读，无法重新分区。进入命令行用`mount -o remount,rw /`，没用，mount显示是rw，但是安装程序认为是read-only。难道是桌面版安装的残留导致？ （切回桌面版的CD，果然就可以重新分区。）
 
 
在命令行中用`fdisk`给dm-0加上启动标志，重启，居然就起来了！！！
 
总结：ubuntu11内置raid5的驱动，但桌面版的安装程序在分区时似乎有bug，第一次没能成功将磁盘mount为/dev/dm-0，却仍是/dev/sda。这时，安装程序默认不给添加启动标志，导致安装后启动不了，其实/etc/fstab并没有问题。唯一不能解释的是，安装了桌面版以后，server版总认为raid磁盘是read-only的。