---
title: "TCP_DEFER_ACCEPT for Linux vs  accept filter for FreeBSD,  tux 还是玩不起来"
author: kxn
type: post
date: 2005-12-09T06:50:29+00:00
url: /post/20.html
views:
  - 2881
categories:
  - Coding
  - Tech Notes
---

Linux 提供的一个特殊 setsockopt ,　在 accept 的 socket 上面，只有当实际收到了数据，才唤醒正在 accept 的进程，可以减少一些无聊的上下文切换。代码如下

> val = 5;  
> setsockopt(srv_socket->fd, SOL_TCP, TCP_DEFER_ACCEPT, &val, sizeof(val)) ;

里面 val 的单位是秒，

**注意如果打开这个功能，kernel 在 val 秒之内还没有收到数据，不会继续唤醒进程，而是直接丢弃连接。**

FreeBSD 有一个 accept filter 的机制，不过看起来更牛一点, 可以注册自己的 handler 来处理连接。系统还提供了默认的 http filter, 接到完整的 http 请求了，再唤醒 accept 进程。

Linux 下面如果需要有东西可以内核态管理 HTTP 协议连接，那就是 tux 了。方汉是 tux 的爱好者。满街弄的都是 tux, 不过这个东西看起来非常需要人品，我跑了两次都造成了很多 oops 。还是算了。。
