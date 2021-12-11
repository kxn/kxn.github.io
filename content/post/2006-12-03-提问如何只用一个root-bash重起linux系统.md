---
title: 提问:如何只用一个root bash重起Linux系统
author: kxn
type: post
date: 2006-12-02T17:54:35+00:00
url: /post/144.html
views:
  - 3589
categories:
  - Network
  - Tech Notes
---

假设你远程连着一个 Linux 系统 shell, 这个 shell 是 root shell, 但是任何其它外部命令都没得用. 如何将这台 Linux 系统重起呢？这个 Linux 是默认安装的 fedora core, 没有自己重新编译 kernel 之类事情。

这个问题的背景是这样的，最近远程升级一台 FC2 -> FC6, 因为 FC3 有 udev 问题，FC4 有 non-ext3 root 问题，所以决定不重起一路冲上 FC6，结果忘记了 FC5 的 glibc 2.4 强迫需要新版本 kernel，于是升到一半就挂了。这时候唯一能用的命令是 bash 内部命令和静态链接的程序，可惜平时没有想到会出现这样的事情，没有储备一个 busybox 之类的东西，系统上面仅有的一些静态链接命令都是 dmraid 之类搞硬盘的。其他命令统统不可用。

这个问题的答案我已经找到了，聪明的读者们，你们知道我是怎么做的吗？请在留言里面发表你的办法.
