title: 玩红米手机随记
date: 2016-04-21 17:50:45
tags: [hongmi, usb, ubuntu]
---

用lsusb来查看usb设备
```bash
lsusb
```

```bash
sudo vi /etc/udev/rules.d/70-android.rules
SUBSYSTEM=="usb", ATTRS{idVendor}=="230b", ATTRS{idProduct}=="0100",MODE="0666"
sudo chmod a+rx /etc/udev/rules.d/70-android.rules
sudo service udev restart
```

```bash
sudo -s
adb kill-server
adb devices
```
