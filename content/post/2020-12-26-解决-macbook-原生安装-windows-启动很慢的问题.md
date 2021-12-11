---
title: 解决 Mac 电脑原生 EFI 安装 Windows 启动很慢的问题
author: kxn
type: post
date: 2020-12-26T13:43:56+00:00
url: /post/500.html
categories:
  - Tech Notes
---

Mac 电脑如果不用 bootcamp，直接用 EFI 方式安装 windows 或者其他操作系统，会发现开机的时候会黑屏或者灰屏停留 30 秒才开始引导。网上搜到的很多帖子都说需要用 recovery 盘启动，用 bless 命令修改 bootloader 参数，实际上这个方法并不适用于原生 EFI 方式，只适用于用 bootcamp 方式安装 bios 启动的操作系统。

真正导致这 30 秒 delay 的原因是 bootloader 找不到原本的 EFI 启动项 MacOSX ，于是它等了 30 秒才去尝试下一个启动项 Windows。于是只要删掉原有的 OSX EFI 启动项就好了。

随便用一个可以在 Windows 下面编辑 UEFI 启动项的软件（我用的是 DiskGenius 免费版本，在工具菜单的最后一项），删掉已经不存在的 OSX 项，一般是 0080，然后保存启动项信息，退出重启就能看到开机立刻出现 Windows logo 了。
