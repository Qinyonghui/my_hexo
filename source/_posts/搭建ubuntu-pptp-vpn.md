title: 搭建 ubuntu pptp vpn
date: 2015-11-08 11:21:35
tags: [vpn, ubuntu]
---

pluto: 实现了IKEv1 (RFC2409)
pluto是一个IKE(ipsec密钥交换)的守护进程，whack是一个辅助程序，运行请求的程序

用自身的IPSEC内核堆栈, KLIPS
或者2.6n内核中的堆栈 26sec  <-用它不用给内核打补丁，就可以用nat

RSA signature 数字签名
生成一个新的RSA密钥

    首先，需要购买一个vps，我使用的是digitalocean提供的服务器，
    1.安装pptpd
