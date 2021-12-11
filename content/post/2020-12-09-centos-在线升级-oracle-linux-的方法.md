---
title: CentOS 在线升级 Oracle Linux 的方法
author: kxn
type: post
date: 2020-12-09T15:33:43+00:00
url: /post/496.html
categories:
  - Tech Notes
---

1：强制安装 oraclelinux-release 和 redhat-release 的包  
从 yum.oracle.com 找到对应版本这两个包的最新版，例如 Oracle Linux 8(对应 CentOs 8) x86_64 这两个包最新版是

[https://yum.oracle.com/repo/OracleLinux/OL8/baseos/latest/x86_64/getPackage/oraclelinux-release-8.3-1.0.4.el8.x86_64.rpm][1]

[https://yum.oracle.com/repo/OracleLinux/OL8/baseos/latest/x86_64/getPackage/redhat-release-8.3-1.0.0.1.el8.x86_64.rpm][2]

用 rpm 强制安装之

`rpm -i --force https://yum.oracle.com/repo/OracleLinux/OL8/baseos/latest/x86_64/getPackage/oraclelinux-release-8.3-1.0.4.el8.x86_64.rpm https://yum.oracle.com/repo/OracleLinux/OL8/baseos/latest/x86_64/getPackage/redhat-release-8.3-1.0.0.1.el8.x86_64.rpm`

2: 删除原有 centos release 包

`rpm -e centos-gpg-keys centos-repos centos-release`

如果有安装 epel-release 也一起删除

`rpm -e epel-release`

3: 安装 oraclelinux-release-el? 的包，比如 centos 8 就用 oracle-release-el8 ，同样从 yum.oracle.com 找到这个包的最新版  
[https://yum.oracle.com/repo/OracleLinux/OL8/baseos/latest/x86_64/getPackage/oraclelinux-release-el8-1.0-14.el8.x86_64.rpm][3]

`rpm -i https://yum.oracle.com/repo/OracleLinux/OL8/baseos/latest/x86_64/getPackage/oraclelinux-release-el8-1.0-14.el8.x86_64.rpm`

(剩下的事情都可以用 yum 来搞定了)

4: 如果之前有安装 epel-release 的话，安装 oracle-epel-release-el? (依然要对应版本)

`yum install oracle-epel-release-el8 -y`

5: yum 升级到最新版本

`yum update -y`

6: 可选：使用 Oracle 打造的专用内核 UEK(Unbreakable Enterprise Kernel)

`yum install kernel-uek`

[1]: https://yum.oracle.com/repo/OracleLinux/OL8/baseos/latest/x86_64/getPackage/oraclelinux-release-8.3-1.0.4.el8.x86_64.rpm
[2]: https://yum.oracle.com/repo/OracleLinux/OL8/baseos/latest/x86_64/getPackage/redhat-release-8.3-1.0.0.1.el8.x86_64.rpm
[3]: https://yum.oracle.com/repo/OracleLinux/OL8/baseos/latest/x86_64/getPackage/oraclelinux-release-el8-1.0-14.el8.x86_64.rpm
