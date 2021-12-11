---
title: Bluetooth on Windows 2003 Ent
author: kxn
type: post
date: 2005-08-21T07:16:11+00:00
url: /index.php/archives/5
views:
  - 3257
categories:
  - Software
  - Tech Notes

---
买了一个蓝牙棒用来和 K700C 手机连接, 厂家提供的驱动配合厂家提供的软件在家里面的 Winxp 和单位的 win2003 上面都可以正常工作，但是[FMA][1] 工具不认厂家提供的驱动，连用厂家工具虚拟串口都不能正常工作，老是死掉。在 XP 上面卸载掉厂家驱动以后，FMa 可以用XP SP2 自带的驱动，Win2003 上面就没招了。

google 了一阵子，[有人][2]说可以用 XP post sp1 的某个 hotfix ， 修改一下装上。该 hotfix 是 [KB323183][3] ， 于是 google 找到 [Q323183\_WXP\_SP2\_x86\_CHS.exe][4] 下来解开，修改 update\update.inf 里面的 max windows build 号成 3790, 也就是 2K3 的 build 号， 可是安装了以后什么反应都没有？

再察看一下该 fix 里面的文件，原来 bth.inf 里面没有写任何 vendorid ，难怪什么都认不出来，于是找了一个 xp sp2 的机器，把里面的 bth.inf 拷贝过来改了改再安装，报文件找不到，敢情这个 fix 里面的一些文件名和 xp sp2 里面的不一样. ft 。仔细核对了一遍，把 xp sp2 里面的对应文件都搞过来，修改 inf 重新安装。搞定。

不过还是有点问题，XP 下面，右下角会显示一个蓝牙小图标。好像是通过 rundll32 bthprops.cpl 执行的进程显示的，但是 2k3 上面这个同样命令行运行会直接退出，什么都不显示，配对设备的时候只能直接运行 bthprops.cpl 手动配对。 

回头有时间把弄好的 bluetooth for 2K3 驱动打个包放上来。现在就算了，而且也不是太完美。

 [1]: http://fma.sf.net
 [2]: http://eben.phlegethon.org/bluetooth.html
 [3]: http://support.microsoft.com/default.aspx?id=323183
 [4]: http://www.google.com/search?hl=zh-CN&q=Q323183_WXP_SP2_x86_CHS.exe&lr=