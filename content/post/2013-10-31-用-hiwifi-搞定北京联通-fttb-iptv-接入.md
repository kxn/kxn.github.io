---
title: 用 HiWifi 搞定北京联通 FTTB IPTV 接入
author: kxn
type: post
date: 2013-10-30T16:01:11+00:00
url: /post/291.html
views:
  - 1
categories:
  - Tech Notes
---

原理在之前的博客 [用一台 MW4530R 搞定北京联通 IPTV FTTB 接入][1] 里面已经讲的很清楚了。这里只贴配置

需要说明两点

一是 openwrt 某个版本的 netifd 已经支持 macvlan 了，然而很可惜不支持在 vlan 就是形如 eth0.999 的设备上用，所以还是需要用 macvlan.sh. pvid 的支持已经正常了，直接改用系统提供的 pvid 设置方式。

二是 hiwifi 放出来的 openwrt 代码是注册两个网卡设备 eth0 和 eth1 的，为了让他们能在一个交换机芯片上交换，需要修改一下代码，让 wan 口也变成 eth0 交换芯片上的一个口。

> config interface 'loopback'  
> option ifname 'lo'  
> option proto 'static'  
> option ipaddr '127.0.0.1'  
> option netmask '255.0.0.0'
>
> config interface 'lan'  
> option ifname 'eth0.1'  
> option type 'bridge'  
> option proto 'static'  
> option ipaddr '192.168.1.1'  
> option netmask '255.255.255.0'
>
> config switch  
> option name 'eth0'  
> option reset '1'  
> option enable_vlan '1'
>
> \# lan  
> config switch_vlan  
> option device 'eth0'  
> option vlan '1'  
> option ports '0t 3 4t'
>
> \# pppoe  
> config switch_vlan  
> option device 'eth0'  
> option vlan '2'  
> option vid '344'  
> option ports '0t 1t'
>
> \# iptv external  
> config switch_vlan  
> option device 'eth0'  
> option vlan '3'  
> option vid '1544'  
> option ports '0t 1t 4t'
>
> \# tr069  
> config switch_vlan  
> option device 'eth0'  
> option vlan '4'  
> option vid '3999'  
> option ports '0t 1t'
>
> \# macvlan on top of eth0.3999  
> config interface 'tr069base'  
> option ifname 'eth0.3999'  
> option proto 'macvlan'  
> option device 'eth2'  
> option hwaddr 'AA:BB:CC:DD:EE:FF'
>
> \# tr069 interface  
> config interface 'tr069'  
> option ifname 'eth2'  
> option proto 'pppoe'  
> option username 'bjcnc-hgw@hgw-nms'  
> option password 's1b5x7z9'  
> option defaultroute '0'  
> option peerdns '0'
>
> \# pppoe interface  
> config interface 'wan'  
> option ifname 'eth0.344'  
> option proto 'pppoe'  
> option username 'username'  
> option password 'password'
>
> \# iptv as default port  
> config switch_port  
> option device 'eth0'  
> option port '1'  
> option pvid '3'

[1]: http://blog.kangkang.org/post/279.html
