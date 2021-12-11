---
title: Fight against cacti, net-snmp and squid
author: kxn
type: post
date: 2006-01-15T12:00:38+00:00
url: /post/64.html
views:
  - 4391
categories:
  - Network
  - Tech Notes
---

一天多来都在折腾这三个东西，作点笔记好了。

首先简单介绍一下:

[cacti][1] 是一个用 [rrdtool][2] 来画图的网络监控系统,  通常一说到网络管理, 大家首先想到的经常是 [mrtg][3], 但是 mrtg 画的图简单且难看, rrdtool 虽然画图本领一流, 画出来的图也漂亮, 但是他也就是一个画图工具, 不像 mrtg 那样本身还集成了数据收集功能. cacti 则是集成了各种数据收集功能,然后用 rrdtool 画出监控图形. 其本身界面比起同类系统要漂亮不少. 推荐所有有监控需求的人都去研究一下.

cacti 和 [nagios][4] 是不同功用的系统, nagios 适合监视大量服务器上面的大批服务是否正常, 重点并不在图形化的监控, 其集成的很多功能例如报警,都是 cacti 没有或者很弱的. cacti 主要用途还是用来收集历史数据和画图, 所以界面比 nagios 漂亮很多.  
[net-snmp][5] 是一套广泛使用在类 unix 系统上的 [snmp][6] 软件, 包含一套 snmp agent 框架 ,一个 snmpd 和 一堆 snmp 工具 , 其前身为 ucd-snmp. 关于 snmp 是什么, 以及如何配置的文章,网上搜一下有一堆一堆的. 在这里就不重复了.

[squid][7] 是一个 web  缓存加速程序, 本来跟监控没有太大关系, 只是因为他支持 snmp 查询,而我要用 cacti 监控他, 然后遇到了他的缺陷被折腾了一阵子,所以也拉进今天的讨论.

我跟这三个东西斗争的过程如下...

首先先把 cacti 架起来,  在架的过程中我没有遇到问题,但是把 czz 搞了一下, 因为 cacti 要调用外部程序, 不能开 safe_mode, 如果开了就会出奇怪问题.

接下来配置 squid 的查询, squid 的查询数据比较多且复杂,自己做模版的话很麻烦,于是 google 了一下,找了一个 SquidStats ([见附件][8]) 的模版, 按照他的 readme 一步一步来, 就可以正常安装. 于是我就遇到了第一个坎...

设置完成以后执行 poller 的时候总是无法产生 rrd 数据, 给 php 里面加 log 也没有看出来什么, google 换了很多关键词, 总算发现了[原因][9]:  cacti 在进行 snmp 查询之前会先确定对方是否在运行, 他用的方法是查询 .1.3.6.1.2.1.1.3.0 这个 oid, 但是 squid 不支持这个 oid , 于是 cacti 就以为 squid down 了,不去真正查询. 临时解决方法是在 cacti 的 settings 里面, poller 页的 <font class="textEditTitle">Downed Host Detection 选择 Ping, 不要选择带有 snmp 字样的.</font>

然后在弄 64 位机的时候遇到了第二个坎,  发现 64 位 linux 机器的流量图总是不正确. 大部分时候没有结果,有时候又特大, 其实这个应该很容易想到是 counter 回绕不正确的问题, 但是我第一次 google 出来的结果是一个说和 tunnel 设备有关的 bug, 这两台   x64 机器上面确实有 tunnel ,于是我就一直以为是 tunnel 的问题. 折腾了好久. 最后才发现原来就是简单的   counter 回绕不正确的问题. 从 Fedora Core 5 的开发目录里面下一个 net-snmp 5.3 的 srpm 在   centos 4.2 上 build 一下, 就搞定了. 注意 FC4 里面的 net-snmp 5.2.x 也是有 bug 的,一定要 5.3 的.

最后推荐所有研究 cacti 的人,一定不要放过 cacti 的官方论坛[扩展脚本版面][10] . 里面有很多的第三方的模版和脚本, 支持很多的网络设备.

[1]: http://www.cacti.net "Cacti"
[2]: http://people.ee.ethz.ch/~oetiker/webtools/rrdtool/ "RRD Tool"
[3]: http://people.ee.ethz.ch/~oetiker/webtools/mrtg/ "MRTG"
[4]: http://www.nagios.org "NAGIOS"
[5]: http://net-snmp.sourceforge.net/ "NET-SNMP"
[6]: http://www.cisco.com/univercd/cc/td/doc/cisintwk/ito_doc/snmp.htm "Simple Networking Management Protocol"
[7]: http://www.squid-cache.org "Squid"
[8]: http://kangkang.org/wordpress/wp-content/uploads/2006/01/SquidStats-0.1.zip "SquidStats"
[9]: http://bugs.cacti.net/view.php?id=644
[10]: http://forums.cacti.net/forum-12.html
