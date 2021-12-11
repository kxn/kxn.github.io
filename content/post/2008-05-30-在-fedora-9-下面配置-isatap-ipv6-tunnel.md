---
title: 在 Fedora 9 下面配置 ISATAP IPV6 tunnel
author: kxn
type: post
date: 2008-05-30T08:49:45+00:00
url: /post/196.html
views:
  - 889
categories:
  - Network
  - Tech Notes
tags:
  - ipv6 isatap fedora
---

Fedora 9 终于换成了 2.6.25 kernel，2.6.25 支持 ISATAP 方式的 ipv6 tunnel 接入。于是把自己的机器简单配置了一下支持 IPV6

1: 保证 kernel 支持 ipv6  
2: 编辑 /etc/sysconfig/network ，增加下面这行

> IPV6_DEFAULTGW=**your ipv6 gateway**

3: 编辑 /etc/sysconfig/network-scripts/ifcfg-sit1, 内容如下

> DEVICE=sit1  
> ONBOOT=yes  
> IPV6INIT=yes  
> IPV6TUNNELIPV4=**your isatap tunnel IP**  
> IPV6TUNNELIPV4LOCAL=**your local ipv4 ip**  
> IPV6ADDR=**your ipv6 address**

4: ifup sit1

UPDATE: 我还没有试验过这个在 F9 上重启是否有效，但在 centos 5.1 上面不好用，似乎 ifup-sit 不会创建对应的 sit1 设备，先得手动创建以后才有效。
