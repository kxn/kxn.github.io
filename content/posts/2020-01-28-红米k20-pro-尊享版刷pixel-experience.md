---
title: 红米K20 Pro 尊享版刷Pixel Experience
author: kxn
type: post
date: 2020-01-27T17:08:28+00:00
url: /index.php/archives/424
categories:
  - Tech Notes

---
折腾了一阵子才搞定，记录一下

首先解锁不用说了，网上多的是，重点是解锁以后的操作。  
先刷 twrp ，重启进 twrp，然后从电脑上把下的 PE zip 包拷贝到手机里面。  
用 twrp install 这个 zip 以后， 正常 Wipe Cache/Dalvik  
之后是重点了！

重要的话要说三遍  
用 twrp Wipe 里面的 Format data 把 internal storage 给格式化一下！  
用 twrp Wipe 里面的 Format data 把 internal storage 给格式化一下！  
用 twrp Wipe 里面的 Format data 把 internal storage 给格式化一下！

这个疑似是因为 K20 Pro 默认把 /data 分区给开启加密了，如果不格式化，/data 和 /sdcard 下面文件名都是乱码，这会导致重启失败直接进 twrp，格式化就可以去掉这个加密的标识。直接 Wipe /data 和 Internal Storage 行不行？不知道，你可以试试。（此处也不要参考网上说的什么刷印度版本底包之类，红米自己固件就是底包，没必要刷其他底包。更不要照着其他教程 Wipe /system ，那样底包就被你清掉了）