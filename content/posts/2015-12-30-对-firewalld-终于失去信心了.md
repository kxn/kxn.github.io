---
title: 对 firewalld 终于失去信心了
author: kxn
type: post
date: 2015-12-30T06:39:12+00:00
url: /index.php/archives/334
categories:
  - Network
  - Tech Notes

---
各种坑，比如 iptables 命令执行一旦出错，后面的不会执行，如果内核少个模块，就会导致防火墙完全封死外面进不去的状态。在 openvz 主机上面简直是灾难。

从其他 fw 框架里面随便挑了一个 shorewall 把他替换掉，一下就清净了。