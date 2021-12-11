---
title: CentOS 7 在线升级 CentOS 8
author: kxn
type: post
date: 2020-06-13T10:10:14+00:00
url: /post/434.html
categories:
  - Tech Notes
---

CentOS7 上的软件逐渐已经开始落后于时代。虽然 CentOS 8 出来的时间还没有那么多，可能还需要很多小白鼠的考验。但是按照以前经验，已经需要开始试用了。

和 6 在线升 7 不一样，7 在线升 8 是没有官方支持的，只是能用而已，所以搞之前务必小心。

参考的基本贴是这里https://www.tecmint.com/upgrade-centos-7-to-centos-8/

操作命令

0: 干啥先备份，死了还有得救

0.5: 请尽可能卸载系统上所有非官方的 rpm 包和 repository，如果安装了第三方内核，并且当前启动在第三方内核下面的话，请重启进入 CentOS 7 原生的 3.10 内核，然后卸载第三方内核的 rpm 包。

0.8: **警告，实体机目前有可能会出现重启没驱动找不到盘的现象，原因还没仔细研究，慎重！**

1: 首先安装 epel-release，已经装过了的请跳过

> yum -y install epel-release

2: 安装 yum-utils (为了使用 package-cleanup) 和 rpmconf

> yum -y install yum-utils rpmconf

3: 首先用 rpmconf 来检查所有 rpm 配置文件

> rpmconf -a

会不断询问一些配置文件你要使用原始的版本（按 Y），还是保留你修改过的（按 N），原则是尽量使用原始的版本，如果影响比较大，比如改了 ssh 端口之外的，就保留自己的

4: 卸载 rpmconf，已经没用了

> yum -y remove python36-rpmconf rpmconf

5: 用 package-cleanup 检查有没有没用的 rpm

> package-cleanup --leaves

这个出来的 rpm 大部分都可以安全卸载，将用不到的卸掉，但是卸载时候注意看会不会有系统关键 rpm 被卸载，有的话跳过就行，这一步不一定都要卸干净

> package-cleanup --orphans

这一步出来的 rpm 一般是内核和你自行安装的第三方 rpm , 看情况卸载，内核可以不用管, 这一步也是不一定要卸载干净

6: 安装 dnf

> yum -y install dnf

7: 卸载 yum

> dnf -y remove yum yum-metadata-parser  
> rm -rf /etc/yum

8: 用 dnf 把系统更新到最新

> dnf -y upgrade

9: 安装 CentOS 8 的基础信息 rpm

> dnf -y install http://mirror.centos.org/centos/8/BaseOS/x86\_64/os/Packages/centos-repos-8.2-2.2004.0.1.el8.x86\_64.rpm http://mirror.centos.org/centos/8/BaseOS/x86\_64/os/Packages/centos-release-8.2-2.2004.0.1.el8.x86\_64.rpm http://mirror.centos.org/centos/8/BaseOS/x86_64/os/Packages/centos-gpg-keys-8.2-2.2004.0.1.el8.noarch.rpm

如果报告文件找不到，那么可能是 CentOS 又升级了，去对应的目录下面找到正确的文件名即可

10: 升级 epel 的 rpm

> dnf -y upgrade http://hkg.mirror.rackspace.com/epel/epel-release-latest-8.noarch.rpm

11: 卸载 CentOS7 内核和冲突的 sysvinit 脚本

> rpm -e \`rpm -q kernel\`  
> rpm -e --nodeps sysvinit-tools

12: 清理 /etc/yum.repos.d 下面的 repo 文件，将 CentOS- 系列 repo 文件都恢复成原始版本(看到有扩展名是 .rpmnew 的，就用 .rpmnew 覆盖掉对应文件) 将 epel.repo 也恢复成原始版本。

13: 进行主要升级

> dnf -y --releasever=8 --allowerasing --setopt=deltarpm=false distro-sync

这步是最容易出问题的，经常会报各种奇怪错误，请参考后面的常见问题

14: 安装内核

> dnf -y install kernel-core kernel-modules

15: 把 minimal 组给装上

> dnf -y groupupdate "Core" "Minimal Install"

16: 找出系统上其余 el7 的 rpm 包，将其卸载

> rpm -qa |grep el7 | xargs dnf remove

会报告所有需要卸载的软件包，记下对你有用的那些，再次执行

> rpm -qa |grep el7 | xargs dnf -y remove

来真正卸载

17: 启用 NetworkManager 并禁用 initscript 的网络初始化，不少 CentOS 7 的安装或多或少是禁用了 NetworkManager 或者依赖于 initscript 的，这种安装如果升级不做这个的话，有可能重启网络起不来。

> systemctl enable NetworkManager  
> systemctl disable network

18: 重启系统，把前面所有卸掉的有用软件包全都装回来，配置文件可能需要手工恢复一下，不用着急，一般都会用 .rpmsave 扩展名保存一个备份的。

常见问题

- 报告会导致受保护的软件包 dnf 被移除  
  一般是 /etc/yum.repos.d 没清理干净，尤其是 CentOS 的 repo 文件是旧的，导致没法升级 dnf，请清理 repo 文件

- 报告会导致受保护的软件包 kernel-ml 被移除  
  是因为你没听我的，在第三方内核启动状态状态下执行了升级，重启进 rescue 内核吧。能不能起得来就看你人品了。

- 报告 anonbin 和 redhat-rpm-config 依赖错误  
  删掉所有 kernel-devel 就好了。（和一个好的机器对比了半天终于找出来的）

> rpm -qa |grep kernel-devel | xargs rpm -e

- 重启以后 pppoe 拨号起不来  
  原因是 CentOS 8 开始强推 NetworkManager 了， 虽然给了 network-scripts 这个包用来保持向下兼容性，但是也就只是能在有线网卡上用用而已，对于 pppoe 等冷门连接方式，你只能用 NetworkManager 了.  
  对于 pppoe 连接，你需要单独安装 NetworkManager-ppp 这个包

> dnf -y install NetworkManager-ppp

一个好消息是 CentOS 8 上面不再需要 rp-pppoe 这个包来做内核 pppoe 了，直接配直接就是可用的。

- Zabbix agent 没了怎么办  
  CentOS 8 上面有 zabbix40 系列的包，可以用 dnf 装这个，但是一般来说 CentOS 7 上还是用 3.x zabbix 比较多，而 4.x 的 agent 是不能和 3.x zabbix 一起用的，所以直接去 Zabbix 的官网下载 3.x 的 rpm 就可以了。
