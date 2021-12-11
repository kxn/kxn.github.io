---
title: 庞大的 .xsession-errors 文件
author: kxn
type: post
date: 2008-09-01T06:37:19+00:00
url: /post/203.html
views:
  - 1047
categories:
  - Tech Notes
tags:
  - .xsession-errors
---

台式机十多天没关机, 忽然发现 fcitx 异常的卡, 把各种应用程序搞死好几次. df 一下才发现是 home 居然满掉了. 仔细 ls -la 一看. 原来是一个 .xsession-errors 文件占了 11G 还多.

看看内容, 原来是所有 X 程序打到 console 上的信息都跑这个文件里面去了, 然后 KDE 程序经常会打印各种 Warning , 用不了多久这个文件就会变得暴大.

解决方法比较土, 直接 ln -sf /dev/null ~/.xsession-errors 了... 可能有什么办法可以搞掉他, 但是似乎没那个必要费脑细胞的说.
