---
title: "RouterOS 解决升级到 7.1 以后 IPv6 不通的问题"
date: 2021-12-17T23:37:17+08:00
author: kxn
type: post
url: /post/559.html
categories:
  - Tech Notes
---

最近发现 RouterOS v7.1 成为 stable 了，于是手贱升级了一下。没想到升级以后 IPV6 完全不通了，表现是本地的 v6 地址和路由上的 v6 地址都能 ping 通，但是 ping 外网就是不通，简单查看了一下没看出来啥问题就赶紧降级回 6.49.2 马上就好了。

开始以为是 RoS 的大 bug，然而晚上越想越觉得不对，这么大一个 bug 感觉不应该留着这么久直接 stable 了。搜了半天，终于找到了一个帖子 https://forum.mikrotik.com/viewtopic.php?f=1&t=178057 ，然后仔细查看了一下 6.49.2 的配置里面，发现也是两条默认路由，然而 6.49.2 就是通的。抱着尝试的心态重新升级到 7.1 ，删除多余的一条路由以后就正常工作了。按照帖子里面所说的，是因为同时启用 dhcpv6 和 pppoe 的 use-default-route 选项造成的，于是去掉 dhcpv6 client 里面的 use default route 选项，保留 pppoe 的，就一切正常。

总之就挺神奇的，原来 6.x 下面这个路由的状态也是不对的，然而就能正常工作，所以从来都没有注意到这里居然也会有问题。
