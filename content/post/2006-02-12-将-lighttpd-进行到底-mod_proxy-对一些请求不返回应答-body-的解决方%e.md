---
title: "将 lighttpd 进行到底,  mod_proxy 对一些请求不返回应答 body 的解决方法."
author: kxn
type: post
date: 2006-02-12T13:31:01+00:00
url: /post/70.html
views:
  - 2656
categories:
  - Network
  - Tech Notes
---

1.4.10 这么快就出来了,号称里面修正了 mod_proxy 处理 30x 请求时候返回 body 不正常的问题, 不过升级了一下发现还是有问题,仔细看了一下,原来本身就是只修正了 301 这一种请求,  而某系统会发送 305 带 body  的结果,于是就不好用了. 自己简单改了一下让他可以支持该系统. 不过 lighttpd 这么干不是个事情啊. 今天搞了 305, 明天说不定还会有不规范的应用用 306,  为啥就一定这么抠门,一点 body 都舍不得发回去哪?

改法:

src/connections.c 第 463 行加上

305 :
