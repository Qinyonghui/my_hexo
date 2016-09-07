title: Raspberry
date: 2016-09-01 21:50:45
tags: [raspberry, linux]
---

```bash
#将树莓派镜像完全备份
dd if=/dev/sdc of=mbr.img bs=446 count=1
dd if=/dev/sdc1 of=boot.img
dd if=/dev/sdc2 of=system.img
```

不要使用下面这条命令
```bash
dd if=/dev/sdc | gzip > raspbian.gz
dd if=/dev/sdc of=raspbian.gz bs=10M
```


备份raspbain镜像
```
$ df
Filesystem     1K-blocks     Used Available Use% Mounted on
/dev/sdb5       19553560  5596276  12940964  31% /
none                   4        0         4   0% /sys/fs/cgroup
udev             8170348        4   8170344   1% /dev
tmpfs            1636124     1712   1634412   1% /run
none                5120        0      5120   0% /run/lock
none             8180608    24056   8156552   1% /run/shm
none              102400       52    102348   1% /run/user
/dev/sda1         184307    74746     95942  44% /boot
/dev/sdb7        4789564     9824   4513400   1% /tmp
/dev/sdb8      590453384 27018088 533418940   5% /home
/dev/sdd          802768   288252    514516  36% /media/qin/F2C9-C682
/dev/sdc1          64456    21248     43208  33% /media/qin/boot
/dev/sdc2       29773020  5655424  22853416  20% /media/qin/2f840c69-cecb-4b10-87e4-01b9d28c231c

$ fdisk -l /dev/sdc
Disk /dev/sdc: 31.1 GB, 31104958464 bytes
64 heads, 32 sectors/track, 29664 cylinders, total 60751872 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x3c8600c2

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            8192      137215       64512    c  W95 FAT32 (LBA)
/dev/sdc2          137216    60751871    30307328   83  Linux

$ df -h /dev/sdc1
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc1        63M   21M   43M  33% /media/qin/boot

$ df -h /dev/sdc2
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc2        29G  5.4G   22G  20% /media/qin/2f840c69-cecb-4b10-87e4-01b9d28c231c

#可以看到两个分区的文件系统
$ mount
/dev/sdc1 on /media/qin/boot type vfat (rw,nosuid,nodev,uid=1000,gid=1000,shortname=mixed,dmask=0077,utf8=1,showexec,flush,uhelper=udisks2)
/dev/sdc2 on /media/qin/2f840c69-cecb-4b10-87e4-01b9d28c231c type ext4 (rw,nosuid,nodev,uhelper=udisks2)

#源分区两个加起来不超过6G，所以创建一个6G大小的空镜像
$ dd if=/dev/zero of=rasp.img bs=1G count=6
6+0 records in
6+0 records out
6442450944 bytes (6.4 GB) copied, 35.6932 s, 180 MB/s

$ du -h rasp.img 
6.1G	rasp.img

#进行分区
$ fdisk rasp.img 
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x871a4406.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 
Using default value 1
First sector (2048-12582911, default 2048): 8192
Last sector, +sectors or +size{K,M,G} (8192-12582911, default 12582911): +64M        

Command (m for help): t
Selected partition 1
Hex code (type L to list codes): l

 0  Empty           24  NEC DOS         81  Minix / old Lin bf  Solaris        
 1  FAT12           27  Hidden NTFS Win 82  Linux swap / So c1  DRDOS/sec (FAT-
 2  XENIX root      39  Plan 9          83  Linux           c4  DRDOS/sec (FAT-
 3  XENIX usr       3c  PartitionMagic  84  OS/2 hidden C:  c6  DRDOS/sec (FAT-
 4  FAT16 <32M      40  Venix 80286     85  Linux extended  c7  Syrinx         
 5  Extended        41  PPC PReP Boot   86  NTFS volume set da  Non-FS data    
 6  FAT16           42  SFS             87  NTFS volume set db  CP/M / CTOS / .
 7  HPFS/NTFS/exFAT 4d  QNX4.x          88  Linux plaintext de  Dell Utility   
 8  AIX             4e  QNX4.x 2nd part 8e  Linux LVM       df  BootIt         
 9  AIX bootable    4f  QNX4.x 3rd part 93  Amoeba          e1  DOS access     
 a  OS/2 Boot Manag 50  OnTrack DM      94  Amoeba BBT      e3  DOS R/O        
 b  W95 FAT32       51  OnTrack DM6 Aux 9f  BSD/OS          e4  SpeedStor      
 c  W95 FAT32 (LBA) 52  CP/M            a0  IBM Thinkpad hi eb  BeOS fs        
 e  W95 FAT16 (LBA) 53  OnTrack DM6 Aux a5  FreeBSD         ee  GPT            
 f  W95 Ext'd (LBA) 54  OnTrackDM6      a6  OpenBSD         ef  EFI (FAT-12/16/
10  OPUS            55  EZ-Drive        a7  NeXTSTEP        f0  Linux/PA-RISC b
11  Hidden FAT12    56  Golden Bow      a8  Darwin UFS      f1  SpeedStor      
12  Compaq diagnost 5c  Priam Edisk     a9  NetBSD          f4  SpeedStor      
14  Hidden FAT16 <3 61  SpeedStor       ab  Darwin boot     f2  DOS secondary  
16  Hidden FAT16    63  GNU HURD or Sys af  HFS / HFS+      fb  VMware VMFS    
17  Hidden HPFS/NTF 64  Novell Netware  b7  BSDI fs         fc  VMware VMKCORE 
18  AST SmartSleep  65  Novell Netware  b8  BSDI swap       fd  Linux raid auto
1b  Hidden W95 FAT3 70  DiskSecure Mult bb  Boot Wizard hid fe  LANstep        
1c  Hidden W95 FAT3 75  PC/IX           be  Solaris boot    ff  BBT            
1e  Hidden W95 FAT1 80  Old Minix      
Hex code (type L to list codes): c
Changed system type of partition 1 to c (W95 FAT32 (LBA))
Command (m for help): p

Disk rasp.img: 6442 MB, 6442450944 bytes
255 heads, 63 sectors/track, 783 cylinders, total 12582912 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x8f8662f7

   Device Boot      Start         End      Blocks   Id  System
rasp.img1            8192      139263       65536    c  W95 FAT32 (LBA)

Command (m for help): n
Partition type:
   p   primary (1 primary, 0 extended, 3 free)
   e   extended
Select (default p): p
Partition number (1-4, default 2): 
Using default value 2
First sector (2048-12582911, default 2048): 139264
Last sector, +sectors or +size{K,M,G} (139264-12582911, default 12582911): 
Using default value 12582911

Command (m for help): p

Disk rasp.img: 6442 MB, 6442450944 bytes
255 heads, 63 sectors/track, 783 cylinders, total 12582912 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x8f8662f7

   Device Boot      Start         End      Blocks   Id  System
rasp.img1            8192      139263       65536    c  W95 FAT32 (LBA)
rasp.img2          139264    12582911     6221824   83  Linux

Command (m for help): w
The partition table has been altered!


WARNING: If you have created or modified any DOS 6.x
partitions, please see the fdisk manual page for additional
information.
Syncing disks.

$ fdisk -l rasp.img 

Disk rasp.img: 6442 MB, 6442450944 bytes
255 heads, 63 sectors/track, 783 cylinders, total 12582912 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x8f8662f7

   Device Boot      Start         End      Blocks   Id  System
rasp.img1            8192      139263       65536    c  W95 FAT32 (LBA)
rasp.img2          139264    12582911     6221824   83  Linux

$ losetup -f
/dev/loop0

$ losetup /dev/loop0 rasp.img

#把img镜像的分区映射出来，这里需要使用kpartx，如果发行版未自带的话需要安装
$ kpartx -av /dev/loop0 
add map loop0p1 (252:0): 0 131072 linear /dev/loop0 8192
add map loop0p2 (252:1): 0 12443648 linear /dev/loop0 139264

#格式化
$ mkfs.vfat /dev/mapper/loop0p1 
mkfs.fat 3.0.26 (2014-03-07)
unable to get drive geometry, using default 255/63

$ mkfs.ext4 /dev/mapper/loop0p2
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done                            
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
389376 inodes, 1555456 blocks
77772 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1593835520
48 block groups
32768 blocks per group, 32768 fragments per group
8112 inodes per group
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done 

$ mount /dev/mapper/loop0p1 /mnt/rasp/partition1/
$ mount /dev/mapper/loop0p2 /mnt/rasp/partition2/

#加.可以拷贝所有文件，包括隐藏文件
$ cp -R /media/qin/boot/. /mnt/rasp/partition1/
$ cp -R /media/qin/2f840c69-cecb-4b10-87e4-01b9d28c231c/. /mnt/rasp/partition2/

$ umount /dev/mapper/loop0p1
$ umount /dev/mapper/loop0p2
```







