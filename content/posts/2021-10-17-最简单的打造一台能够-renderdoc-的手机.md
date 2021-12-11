---
title: 最简单的打造一台能够 RenderDoc 的手机
author: kxn
type: post
date: 2021-10-17T14:48:14+00:00
url: /index.php/archives/553
categories:
  - Tech Notes

---
最近为了倒腾一个能够无痛 RenderDoc 的手机颇花了不少力气，编译了不少固件。在这里记一下结论希望对别人能有点用处。

手机类型建议用高通芯片的小米，lineageos 或者 pixelexperience 能刷的。 可以在这两个固件的官方网站上查询一下是否支持。

拿到以后解锁然后直接刷 lineageos 或者 pixelexperience，不要在官方固件上浪费时间，官方固件虽然可以也可以用 magisk root，但是动态改配置以后工作会不正常。也许必须得 userdebug 的固件才行吧。

刷好以后安装 magisk，然后在 magisk 里面安装 magisk hide props 插件，之后 adb shell 进去

`props ro.secure 0`

按 y 确认然后按 e 退出（注意第一次执行时候会弹 su 的框，记得确认掉）

`props ro.debuggable 1`

按 y 确认以后按 r 重启，重启以后手机就可以完全正常的跑 RenderDoc 了，因为 adb 是 root 方式跑的，所以甚至都不会警告你 apk 不能调试。