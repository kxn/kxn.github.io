---
title: mod_extract_forwarded for lighttpd
author: kxn
type: post
date: 2005-12-19T10:57:03+00:00
url: /index.php/archives/44
views:
  - 3771
categories:
  - Coding
  - Tech Notes

---
今天花了一点时间，用 lighttpd 的 mod\_skeleton.c 划拉了一个 mod\_extforward.c 出来，功能类似 Apache 上面的 mod\_extract\_forwarded.c ，都是用来从 X-Forwarded-For 里面截取出来对方真实 IP 用，特别适合在 squid 后面的机器用，或者有人像我一样，爱好用 mod_proxy 穿来穿去的。不过这个 mod 功能弱很多就是了。

使用方法很简单 , 编译出来这个 module 并复制到 lighttpd 的 lib 目录里面。

在 lighttpd.conf&nbsp; 的 server.modules 里面加上 mod_extforward

配置类似下面这样

extforward.forwarder = ("10.10.10.1" => "trust" , "192.168.1.2" => "trust" )

会自动跳过所有 trusted IP . 

另外 由于 lighttpd 本身 plugin 结构的局限，加载 mod\_extforward 需要在所有模块之后，否则其他都可以正常工作，但是 access log&nbsp; 打出来的对方地址还是 proxy 的。此外还有局限就是，$HTTP["REMOTEIP"] 的变量不会被 mod\_extforward 影响，这个没有办法，对这个的判断先于所有的&nbsp; plugin 加载。

&nbsp;<a target="_self" href="http://kangkang.org/wordpress/uploads/mod_extforward.c" title="代码下载">下载地址</a> 

Update: 修正了和条件语句冲突的情况。