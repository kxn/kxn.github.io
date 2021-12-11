---
title: This site is now running lighttpd + php(fcgi) + eaccelrator
author: kxn
type: post
date: 2005-12-03T07:13:06+00:00
url: /index.php/archives/14
views:
  - 2360
categories:
  - Network
  - Tech Notes

---
scaner 推荐的东西通常都是不错的，这次的 lighttpd 也不例外。

我的站点虽然在跑 php , 但是大多数时候，还是用来下载的比较多，这时候 php 也没啥用处，多占不少内存，lighttpd + phpfcgi 的速度看评测也不慢多少.

不过这东东一启动，就跑了 5 个 php 进程出来，然后每个 php 进程又 fork 一个, 看起来很乱，要是 fcgi 方式还支持 multithread 就好拉

最后顺手又装了 eaccelator 玩，不过这个在我这里实在没有太大用处了是。