---
title: Google Pixel3 (XL) 开启电信 + VoLTE 支持之最干净的改法
author: kxn
type: post
date: 2019-07-08T13:24:46+00:00
url: /post/382.html
categories:
  - Tech Notes
---

Pixel3(XL) 开启电信和 VoLTE 支持网上有很多改法，但是大部分都对系统伤害过大，或者使用的版本过老。自己倒腾了半天大概搞明白了，于是这里就是对系统最小的改法了。

开开发模式，允许 adb 什么的设置就不讲了。

先解锁，这一步会丢数据，注意了。

adb reboot bootloader  
等重启进 bootloader 以后  
fastboot flashing unlock  
等界面上提示以后，用音量键上选择确认解锁。

接下来，从 https://developers.google.com/android/images 下载对应机型最新的版本，解开以后用 flash-all.bat 把手机整个刷了，注意，依然会清除所有数据，如果不想清除，请自行编辑 flash-all.bat 去掉 -w 参数

接下来从 https://pan.baidu.com/s/1KMtYGaqnlDPWFZKvbde9PA 下载 vbmeta.img  
fastboot --disable-verity --disable-verification flash vbmeta vbmeta.img

接下来 https://twrp.me 下载对应机型的最新版本。  
fastboot boot twrp-<版本>.img  
进入以后如果要输密码，选 cancel，之后 enable modification ，然后进去以后我用的版本不会自动启 adb, 我是在 Advanced 里面选 adb sideload 以后取消，他就启动起来了。别的版本可能没有这个问题

接下来 adb shell 进去执行命令  
mount /dev/block/by-name/vendor_a /vendor  
退出  
adb pull /vendor/rfs/msm/mpss/readonly/vendor/mbn/mcfg_sw/mbn_sw.txt  
编辑 mbn_sw.txt  
最后一行加上  
mcfg_sw/generic/China/CT/Commercial/VoLTE_OpenMkt/mcfg_sw.mbn  
然后  
adb push mbn_sw.txt /vendor/rfs/msm/mpss/readonly/vendor/mbn/mcfg_sw/  
如果不需要 VoLTE，只需要电信支持，那么改到这里就已经可以了，如果还需要 VoLTE  
那么继续  
adb pull /vendor/build.prop  
编辑 build.prop  
最后面加上  
#VoLTE  
ro.mtk_ims_support=1  
ro.mtk_volte_support=1  
persist.mtk.volte.enable=1  
persist.dbg.volte_avail_ovr=1  
persist.dbg.ims_volte_enable=1  
persist.dbg.volte_avail_ovr=1  
persist.dbg.vt_avail_ovr=1  
persist.dbg.wfc_avail_ovr=1  
persist.radio.rat_on=combine  
persist.radio.data_ltd_sys_ind=1  
persist.radio.data_con_rprt=1  
persist.radio.calls.on.ims=1

然后  
adb push build.prop /vendor

最后  
adb shell umount /vendor

在 twrp 的界面里面重启，注意选那个 Do not install， 不要滑动

重启以后如果没有效果，在系统选项里面恢复出厂设置即可

用这个方案，对系统只有三处修改，算是最干净的修改方式了，而且使用的是最新版本的 pixel 软件

一共就修改了  
1： 解锁 bootloader  
2: 修改了 vbmeta 分区，禁用了 dm-verity  
3: 修改了 vendor 分区，加入了电信的配置，并且打开了系统设置里面 VoLTE 的开关。
