---
title: 搞掉lighttpd好多LAST_ACK的状态了
author: kxn
type: post
date: 2005-12-19T03:51:37+00:00
url: /post/43.html
views:
  - 3477
categories:
  - Tech Notes
---

终于想通既然只有 CLOSE_WAIT 状态会到 LAST_ACK ，不如干脆堵住 CLOSE_WAIT 产生的源头。于是关掉 lighttpd 的 keep alive , 果然一下就几乎没了。  
不过不支持 keep alive 的 client 真的对 keep alive 的 server 杀伤这么大吗？小 s 家的 lig 上面就几乎没有 LAST_ACK 。为啥就我这里有。。
