title: Initrd.img学习笔记1
date: 2016-03-18 23:02:22
tags: [linux, boot, initrd]
---

　　先来一段百度百科。
　　initrd: (Init RAM Disk),是Linux系统引导过程中的一个临时根文件系统。但在很多嵌入式Linux系统中（比如说Android），initrd就是最终的根文件系统。

　　在ubuntu中，initrd在/boot下，通常命名是initrd.img-xxx(xxx是对应kernel的版本号)，如initrd.img-3.16.0-38-generic

可以看一下它的文件格式
``` bash
$ file initrd.img-3.16.0-38-generic 
initrd.img-3.16.0-38-generic: gzip compressed data, from Unix, last modified: Sat Oct 24 11:41:44 2015
```
它是一个gzip格式的压缩文件
我将它解压出来
``` bash
$cp initrd.img-3.16.0-38-generic /tmp/initrd.img-3.16.0-38-generic.img.zip   //gunzip 识别文件后缀，要不然解压不了。。
$gunzip -d initrd.img-3.16.0-38-generic.img.gz
$file initrd.img-3.16.0-38-generic.img 
initrd.img-3.16.0-38-generic.img: ASCII cpio archive (SVR4 with no CRC)
```
终于看到真面目了，所以initrd.img-xxx 是cpio归档格式文件的压缩包。

　　这里再科普一下CPIO，我在研究initrd以前从来没接触过cpio。
　　cpio 是一个非常有用的备份工具，它最大的不同处，是 可以备份从标准输入（ stdin ）而来的的档案 。 目前有些新版的 tar 支持 从标准输入来的档案备份，但大部份尚未有此功能。 
　　为什么cpio较不常被使用？ 
　　cpio有时支持 I 及 O；有的版本则无。如果将 cpio 每种版本的选项累计，则有近 40 个选项， 甚至有些使用同一字母的参数，在不同版本的 Unix 有不同的意义。 但您只要使用在大部份平台皆标准的选项，那么，cpio 几乎与 tar 一般简单好用。

接着我们用cpio命令从archives中读出文件,一个我们熟知的根文件系统出现在我们面前。
``` bash
$cpio -idmv < initrd.img-3.16.0-38-generic.img
$ls
bin   etc   initrd.img-3.16.0-38-generic.img  lib64  sbin
conf  init  lib                               run    scripts
```

