---
title: 'RHEL/CentOS/OracleLinux 8  yum 能看到，安装却没有的情况'
author: kxn
type: post
date: 2021-03-05T07:15:00+00:00
url: /index.php/archives/529
categories:
  - Tech Notes

---
最近发现 CentOS 强制升级到 OracleLinux 8 以后有些包用 yum search 可以看到，但是 yum install 却说没有。比如 golang 就是这样的。

简单研究了一下，是因为 RHEL8 里面引入了 module 的概念，可以把一些软件包给做成 module , 可以自己选择用哪个分叉，比如 gcc8 和 9 会依赖完全不同的两套软件包，这就可以做成两个不同版本的 module 。 yum search 能看到，但是安装不了就是因为 module 信息乱了，选了一个不存在的版本。

解决方案是 yum module reset <module name> ， 有哪些 module 可以 yum module list 看，比如 golang 的就是 go-toolset，执行 yum module reset go-toolset 以后， yum install golang 就成功了。