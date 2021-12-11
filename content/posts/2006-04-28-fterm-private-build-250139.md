---
title: FTerm private build 2.5.0.139
author: kxn
type: post
date: 2006-04-27T16:17:39+00:00
url: /index.php/archives/108
views:
  - 3101
categories:
  - Tech Notes

---
自己 build 着玩的，界面上面有些修改的功能还没有实现，算是个半成品。看到这个文章的人自己用用就好了，不用传播，有 bug 欢迎反馈，可以在本帖后面留言。

ChangeLog:

* 去掉 alt+数字 切换窗口时候的当当声  
* 去掉对 convcode.dll 的依赖，这个 dll 实在是多余  
* 原来的 idlehook.dll 会被有些杀毒软件识别为木马，用自己写的 idlemon.dll 替换掉了，其实这个东西只在 9x 下面有用。  
* 更新了一下 IP 数据库，QQWry.dat 居然从 3M 变成 5M 了。。

此外虽然没有特别改代码，但是似乎这个 build 在连接 ssh2 站点断开以后出现非法操作，导致无法关闭 fterm 的现象少了很多。如果你能够找到较大概率复现这个 bug 的方法请和我联系。

[教育网下载][1] 

[网通下载(海外用户)][2]

 [1]: http://comman.org/wordpress/uploads/2006/04/fterm139.rar
 [2]: http://kangkang.org/wordpress/uploads/2006/04/fterm139.rar