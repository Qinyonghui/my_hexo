title: Grub学习笔记
date: 2016-02-21 17:50:45
tags: [boot, grub, ubuntu]
---

　　前些日子我装机装系统的时候，先装了ubuntu，后装了windows，导致最后ubuntu加载不起来，用easybcd引导系统也不尽如人意；后来接触到了grub，觉得是个很神奇的东西，研究了一下。
最终对Grub有了最深刻的认识是通过大神Bean_lee的博客，在此推荐给大家，[http://blog.chinaunix.net/uid-24774106-id-3497929.html](http://blog.chinaunix.net/uid-24774106-id-3497929.html)


　　**GRUB**(GRand Unified Bootloader)是一个来自GNU项目的多操作系统启动程序，它是一个多重操作系统启动管理器。用来引导不同系统，如windows，linux

　　既然它是一个引导程序，我们来看下它所处的位置。(盗图一张，M. Tim Jones在Inside Linux boot process中绘制)
　　![](mark/boot_flow.gif)

　　Grub的工作分为两个阶段，分别为图中的stage1和stage2。但在实际应用中，其实还有第三个阶段的存在:stage1.5，下文为你揭晓。
　　stage1、stage1.5和stage2其实也就是三个镜像，依次被加载运行；那么为什么要分三个镜像呢，做一个镜像就搞定岂不是简单愉快？

　　这可能需要看一下BIOS的源码才能知道原委。
　　我没有看过BIOS的源代码，但是我推测一定是因为BIOS在最后会将MBR中的内容搬运到内存中运行，可是MBR只有512Bytes空间，不够放下所有的镜像，所以要分成三个阶段。

　　这里要补充一下MBR的知识:
　　**MBR**，全称为Master Boot Record，即硬盘的主引导记录。
　　为了便于理解，一般将MBR分为广义和狭义两种：广义的MBR包含整个扇区（引导程序、分区表及分隔标识），也就是上面所说的主引导记录；而狭义的MBR仅指引导程序而言。
　　硬盘的***0柱面、0磁头、1扇区***称为主引导扇区（也叫主引导记录MBR）。它由三个部分组成，主引导程序、硬盘分区表DPT（Disk Partition table）和分区有效标志。在总共512字节的主引导扇区里主引导程序（boot loader）占446个字节，第二部分是Partition table区（分区表），即DPT，占64个字节，硬盘中分区有多少以及每一分区的大小都记在其中。第三部分是magic number，占2个字节，固定为0xAA55或0x55AA，这取决于处理器类型  ，如果是小端模式处理器  （如Intel系列），则该值为0xAA55；如果是大端模式处理器 （如Motorola6800），则该值为0x55AA。
　　![](mark/MBR_struct.png)

　　所以放在MBR中的就是stage1了，BIOS将它搬运到0x7c00开始执行，那么它会干什么的呢？
　　**这段512个字节代码的作用是将0柱面，0磁道，2扇区的512字节copy到0x8000处执行**   [预知详情，请见该处](http://blog.chinaunix.net/uid-24774106-id-3497929.html)

　　那放在0柱面，0磁道，2扇区的自然就是stage1.5(准确的说是stage1.5的一部分)了，大小同样也是512个字节，它的存在是为了找到/boot/grub/stage2，而stage2存在文件系统中；我们知道stage1阶段是没有os的，也没有文件系统的概念；识别文件系统的工作就是stage1.5完成的。但是仅仅512bytes大小的程序是完不成对文件系统的操作的，它只是一个引子，作用是从LBA 扇区号2（0柱面 0磁道 3扇区） 开始拷贝若干个扇区到内存，后面的部分才是真正操作文件系统的部分。
　　但是文件系统千千万，我们不可能把所有文件系统的功能文件放在磁盘的扇区里面，那怎么办呢？grub 执行setup的时候，能够识别启动设备的文件系统，比如我们，是ext4文件系统，所以只需要将ext4部分的e2fs_stage1_5放入扇区。

　　到这里，stage1.5已经找到了stage2，stage2就可以开始加载操作系统了。

所以，最后再盗用一下总结：
　　1.MBR code 部分和/boot/grub/stage1部分一样，这部分二进制文件是有grub源代码中的/stage1/stage1.S汇编出的。所谓的stage1,作用只有一个，就是将磁盘第二个扇区的内容加载的到内存
　　2.第二个扇区的内容和/boot/grub/e2fs_stage1_5文件的前512字节一样，这部分内容是有grub 源码/stage2/start.S汇编出的，而这个start.S的作用就是加载磁盘的第三个扇区到第N个扇区到内存，N取几，取决与文件系统的支撑代码的大小。
　　3.文件系统支撑代码到内存之后，我们在也不需要直接调用INT 13加载扇区内容，我们有了文件系统，我们可以直接操作文件了。那么/boot/grub/stage2这样的比较大的文件可以直接操作了。
