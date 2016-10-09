title: 通过losetup挂载文件系统
date: 2016-10-09 21:33:05
tags: [losetup, filesystem,  linux]
---

1.可以在linux上建立镜像，并创建文件系统
===
```
dd if=/dev/zero of=file.img bs=1k count=10000
mkfs -t ext4 file.img
```

2.Android 或者 linux上创建loop设备
===
```
#查看loop设备的设备号
cat /proc/devices
#创建一个loop设备,创建出来的设备可以通过rm -rf来删除
mknod -m 0660  /dev/loop0 b 7 0
```

3.绑定loop设备和文件
===
```
losetup /dev/loop0 file.img
```

4.挂载文件系统
===
```
#挂载完成后/mnt/point目录可以读写访问了
mount -t ext4 /dev/loop0 /mnt/point
```

