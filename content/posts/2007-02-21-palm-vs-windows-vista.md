---
title: Palm vs Windows Vista
author: kxn
type: post
date: 2007-02-21T07:39:20+00:00
url: /index.php/archives/149
views:
  - 2505
categories:
  - Tech Notes

---
前几天把座机换成了 Windows Vista， 然后就很郁闷的发现 Palm 软件工作不正常了，Hotsync 可以正常工作，但是 QuickInstall 一执行就非法操作。Palm 公司号称说这个问题我们知道，正在修复中。本来想等等官方修复的，糟糕的是一个月前刚刷过 Treo ROM，当时懒得破解，现在 Treo 里面软件开始纷纷过期。没有 QuickInstall 的日子简直没法过，网上搜了半天似乎没有发现任何关于这个的解决办法。因此只好自己动手丰衣足食。

经过一整天的 Debug，最后大致确定了根本原因还是在 Vista 的 Isolation 上面。 Isolation 本来不是什么新鲜的概念，在 XP 上面就有，最一开始似乎只是为了解决 DLL Hell 的问题，隔离环境里面可以只看到 manifest 里面指定要的 DLL 版本。在 Vista 上面 Isolated 环境之间真正被隔离开来，包括 COM 类型库注册都可以单独注册进不同的环境，不同环境之间的通信受到访问权限的控制。Palm 就是死在这个上面了，他的安装程序将 COM 组件都注册进了用户原本的环境里面，HotSync 可以正常执行，是因为他不需要提升后的权限，而 QuickInstall 不知道什么地方需要使用 Admin 权限，于是他实际是在提升后的环境里面运行，就找不到当时安装注册的 COM 组件，从而出错了。

有人可能会问关闭 UAC 了还是不行么？对，关闭 Vista 的 UAC 不能解决这个问题，因为 Vista 即使关闭 UAC, 程序一开始还是在低权限的原始环境下面执行，只是如果他需要更高权限时候，会自动提升权限而不弹出确认而已，提升之后的环境和打开 UAC 点确认的环境是一样的。

解决方案也比较土，首先既然 QuickInstall 一定需要在管理员权限下面跑，那么我们需要把所有 Palm 的 COM 组件在 Administrator 下面重新注册一下。

用管理员身份运行 cmd 命令行

cd "c:\program files\palmone"  
for %a in (*.dll) do regsvr32 /u /s %a  
for %a in (*.ocx) do regsvr32 /u /s %a  
for %a in (*.dll) do regsvr32 /s %a  
for %a in (*.ocx) do regsvr32 /u /s %a

之后 QuickInstall 应该就可以运行了，但是如果 UAC 是打开的，在 explorer 里面双击 .pdb 和 .prc 的时候会出错，这个是因为跨环境的 DDE 被 Vista 禁止了，我不知道如何在 UAC 打开的情况下面绕过这个，所以只好关闭 UAC。此外在 HotSync 的时候会出错，安装不进去，解决办法是在 HotSync 的同时还要保持 QuickInstall 程序处于运行状态，原因不明。

经过这么一番折腾，总算可以在 Vista 下面凑合安装 Palm 软件了。最后的解决方案实在不能算上是完美，又要关闭 UAC，又要在 HotSync 的时候保持 QuickInstall 程序是打开的，还是等 Palm 出官方的修正比较好，在那之前就用这个先凑合凑合吧。