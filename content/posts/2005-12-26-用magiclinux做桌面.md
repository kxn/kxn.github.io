---
title: 用MagicLinux做桌面
author: kxn
type: post
date: 2005-12-26T15:36:48+00:00
url: /index.php/archives/52
views:
  - 2876
categories:
  - Software
  - Tech Notes

---
很久没有用过 Linux 作桌面了，在很久很久以前，用Linux做桌面的不是英文小神童，就是忍者。那时候的Linux桌面不调理上一个星期很难到一个顺手的境界。配声卡，配网卡，配X/显卡，配中文，配WM，哪个都得折腾一阵子。所以除了只编程不需要任何娱乐的哥们，很少有人专门用Linux做桌面。后来时代发展了，一方面Linux驱动不断增加，另一方面硬件厂商不断兼并，到现在 Linux 对主流硬件的支持已经算比较好了。但是中文支持还是一样的衰。大部分dist的中文显示都发虚，各种软件里面默认设置的编码都不是中文，等等。虽然说这些事情都可以自己慢慢装各种补丁，改系统配置之类搞定，但是我还是不太喜欢用Linux来作桌面，毕竟 OS 是拿来用的，不是用来死磕的。

[MagicLinux][1] 是中国人改的一个RPM based Linux 发行版，我比较喜欢他的原因是开发者比较正常，不会像opendesktop之流拼命追求界面像Windows，结果弄到不伦不类，也不像红旗之流面向政府机构里面不会用Linux的人，弄出来一些功能很弱的web管理界面。

MagicLinux 目前的版本是 2.0 rc2 ，只有一张安装盘，安装的时候没有的可选，只能全装，而且 installer 还有不少不足之处，比如不支持 USB 光驱和网络安装，没有 IDE 光驱的人只能用 vfloppy + 硬盘安装。他的 kernel 默认是加了 ntfs 支持的，installer 还会在安装时候检测所有的分区，并把所有 Windows 分区都 mount 到 /mnt 下面很多目录下面，可写的分区都 mount 成 umask = 0 的，我很喜欢这一点。

MagicLinux 另一点很令人欣赏的地方是他非常新潮，系统自带的软件通常都是一些你以前可能很少听说过，但是确实很好用的软件。我最初见到 Beep Media Player 就是在 ML 1.0 里面。那时候没有一个 dist 里面默认带 BMP。ML 1.2 则是直接用了 KDE 里面的 amarOk,。 此外, ML 里面还针对国内上网需求，内置了很多只有国人才用得着的软件，比如 BT 客户端，SCIM, eva(QQ客户端) 。装好 ML 以后基本什么都不用改，就可以直接用来上网听歌聊天了。

MagicLinux 的开发者是一个 KDE 爱好者，默认设置的各种工具大部分都是 KDE 里面的，当然KDE也确实争气，颇有几个软件还挺好用的，相比起来gnome真是一个扶不上墙的阿斗，做了这么多年，拿的funding比KDE也多了不少，结果到现在还是基本没有什么特别好用的东西，只能靠各种零散用 gtk 的程序来撑场子。人家 KDE 非常完备的一套玩意都出来了，上到KOffice, 下到 KHTML 。

这篇文章就是在 MagicLinux 下面，用 Konqueror ，SCIM 贴上来的。同时用 amarok 放歌，用 k3b 刻 DVD，用 eva 上 QQ, 用 Kopete 上 MSN 。

 [1]: http://www.magiclinux.org