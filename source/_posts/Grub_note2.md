title: Grub 安装配置
date: 2016-10-08 22:07:45
tags: [grub, ubuntu]
---

0.安装grub
===
```
apt-get install grub
```

1.执行grub命令
===
```
#设置启动分区, stage1,stage1.5和stage2所在的位置
grub>root (hd0,0)
#将grub安装到mbr
grub>setup (hd0)
grub>quit
```

2.配置menu.lst
===
```
vim /boot/grub/menu.lst
```
```
title           Ubuntu 14.04.3 LTS, kernel 4.2.3-040203-generic
        root (hd0,0)
        kernel  /vmlinuz-4.2.3-040203-generic root=/dev/sdb5 vga=0x34D ro quiet splash
        initrd  /initrd.img-4.2.3-040203-generic

title   Windows 10
        rootnoverify (hd0,2)
        chainloader +1
```


关于sdax 和 (hd0,x)的区别
===
引用：http://blog.chinaunix.net/uid-26746242-id-3165146.html

问：grub里面的命令有的一句里面有（hd0,x）,还有sdax,为什么要用两种表示方法？
答：一块硬盘最多只能有四个主分区。其中一个可以为扩展分区（主分区的特殊形式），扩展分区中可以有60个逻辑分区。linux表示分区，与顺序和类型有关。
/dev/sda1为第一主分区
/dev/sda2为第二主分区
/dev/sda3为第三主分区
/dev/sda4为第四主分区
/dev/sda5为第一逻辑分区
/dev/sda6为第二逻辑分区
……
不管有几个主分区，逻辑分区都是从/dev/sda5开始的。也就是说，第一个逻辑分区是/dev/sda5。
而四个主分区，可能只有一个，或二个。而且不一定是从/dev/sda1开始的。

一般以前的DOS和windows系统（2000/XP/2003，不包括windows7），使用自带的分区工具，只能分一个主分区和一个扩展分区（内含多个逻辑分区）所以，默认情况下（不是一定，可以改变的），C盘为/dev/sda1，D盘为/dev/sda5，E盘为/dev/sda6……

其中/dev/sda为第一块硬盘
/dev/sdb为第二块硬盘（如果有的话），分区表示法后面的数字一样。

grub是linux系统的启动管理器，也是根据分区顺序表示，但有不同。是以0开始的，不是以1开始的。
(hd0,0)为第一主分区
(hd0,1)为第二主分区
(hd0,4)为第一逻辑分区（通常为D盘）
(hd0,5)为第二个逻辑争区（通常为E盘）
……
(hd0)为第一个硬盘
(hd1)为第二个硬盘

grub2是新的启动管理器，表示方法与grub有些微妙但很重要的不同
(hd0,1)为第一主分区
(hd0,2)为第二主分区
(hd0,5)为第一逻辑分区（通常为D盘）
(hd0,6)为第二个逻辑争区（通常为E盘）
……
(hd0)为第一个硬盘
(hd1)为第二个硬盘
注意，grub与grub2表示的分区方法不同

linux下，只有第一主分区、第二主分区、第三主分区、第四主分区、第一逻辑分区、第二逻辑分区、第三逻辑分区……


以下为常见的G版XP系统，快速分四个分区方式，在linux系统中和grub的表示法（但不保证准确）
C 盘/dev/sda1 (hd0,0)
D盘/dev/sda5 (hd0,4)
E盘/dev/sda6 (hd0,5)
F盘/dev/sda7 (hd0,6)
