title: PPTP Client 配置
date: 2016-06-15 21:21:45
tags: [pptp, vpn, ubuntu]
---

0.安装pptp
``` bash
apt-get install pptp
```

1.配置pptp
``` bash
#service pptpd restart
#iptables -F
pptpsetup --create vpn --server xxx.xxx.xxx.xxx --username ????? --password ****** --encrypt --start
```

2.配置路由
``` bash
#service pptpd restart
VPN_Server=xxx.xxx.xxx.xxx

route > /tmp/.route.tmp
grep -rn "default" /tmp/.route.tmp | awk '{print $2}'>/tmp/.default_gw  
read default_gw < /tmp/.default_gw
echo $default_gw
#default_gw=172.6.1.1

#modify route
route add -host $VPN_Server gw $default_gw dev eth0
route del default
route add -net 0.0.0.0 dev ppp0

#add a periodic visit to vpn, to keep the ppp
#ping www.baidu.com -i 15 
```

3.关闭pptp
``` bash
pptpsetup --delete vpn 
```

4.恢复路由
``` bash
#close l2tp
#echo "d DO" > /var/run/l2tp-control

#close ipsec
#ipsec auto --down DO

#recover route
read default_gw < /tmp/.default_gw

route del default
route add -net 0.0.0.0  gw $default_gw dev eth0
```
