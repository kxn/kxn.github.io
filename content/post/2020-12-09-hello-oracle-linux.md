---
title: Hello Oracle Linux
author: kxn
type: post
date: 2020-12-09T14:12:58+00:00
url: /post/492.html
categories:
  - Tech Notes
---

为了响应红帽给 CentOS 判的死刑，本站今晚成功在线升级到 Oracle Linux 8，完全平滑无障碍。

> [root@blog ~]# cat /etc/oracle-release  
> Oracle Linux Server release 8.3  
> [root@blog ~]# cat /etc/redhat-release  
> Red Hat Enterprise Linux release 8.3 (Ootpa)  
> [root@blog ~]# uname -a  
> Linux blog.kangkang.org 4.18.0-240.1.1.el8_3.x86_64 #1 SMP Mon Nov 9 16:26:47 PST 2020 x86_64 x86_64 x86_64 GNU/Linux

升级完了才发现原来 CentOS 8 摸鱼很久了，内核原来落后了这么多。

然后刚才又手贱了一把，升级了一下 Oracle 自己的内核，号称 Unbreakable Enterprise Kernel。。。  
虽然听起来非常臭屁，但是至少看起来可以正常工作的样子

[root@blog-tx ~]# uname -a  
Linux blog-tx.kangkang.org 5.4.17-2036.100.6.1.el8uek.x86_64 #2 SMP Thu Oct 29 17:06:00 PDT 2020 x86_64 x86_64 x86_64 GNU/Linux
