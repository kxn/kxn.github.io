---
title: 在 LinkSYS WRT54G 上面用 OpenWRT
author: kxn
type: post
date: 2006-04-01T14:34:00+00:00
url: /post/90.html
views:
  - 9001
categories:
  - Tech Notes
---

不是我不知道，这世界变化快，家用无线路由器上面可以跑 Linux 了也。

以前用的无线路由是一台 TP-Link WR541G ，可能是因为 CPU 比较弱的原因，BT 时候线程不能开太多，否则就算带宽还没有打满，网络反应也会很慢，上 bbs 都一卡一卡的。最不能忍的是 TCP NAT 的超时时间非常短，只有五分钟，而且在超时的时候不会给两方发 RST ，对我这样经常开很多 SCRT 窗口干活的人来说实在是太恶心了，很容易就造成很多 shell 尸体。虽然 SCRT 可以设置 anti idle ，但是我一百多个已有的 session，一个一个改过来会累死人的。。

前几天在网上看到了 [OpenWRT][1]，是一个可以运行在 linksys 等一系列家用无线路由器上面的 linux 发行版。Linksys 本身的 firmware 就是基于 linux 的，后来 linksys 不知道为啥一高兴就放出来源代码。于是网上冒出了各种打造版本的 firmware ，但是都还是在 linksys 版本上面的修修补补，每次修改都是生成整个的 firmware 文件刷进路由器。OpenWRT 的开发者觉得这样很麻烦，于是他们选择了不同的一条路，从一个最小的嵌入 linux 开始，一点一点增加功能进去，尽量向 linksys 版本的功能靠近，现在的 OpenWRT 可用性已经相当不错，除了 web 界面都是调用的 shell ，速度比较慢之外，剩下的无线路由基本功能都具备，而且因为是 linux 的缘故，可以自己加入更多好用的功能比如用 linux-igd 支持 upnp, 用 openvpn 把你的路由器和工作单位的机器打通 vpn 连接等等。

不是所有的 linksys 路由器都能支持 OpenWRT, [这个页面][2] 里面有所有已知支持的无线路由器列表，里面除了 linksys，还有不少其他体系结构相近的或者直接就是 linksys OEM 的产品。这个列表里面没有 TP-Link 的 WR541G ，我找人借来一台 linksys WRT54G （TP-Link 的型号跟 Linksys 的也太像了一点，让人不能不怀疑 TP-Link 有混水摸鱼之嫌），打算刷坏了就还给人家，刷好了就把 TP-Link 还给他 :8，还好最后没有出现那种比较不好的情况，呵呵。

OpenWRT 的安装说明在 <http://wiki.openwrt.org/OpenWrtDocs>，虽然现在 OpenWRT 的最新版本 RC5 已经相当不错，安装非常简单，但是我还是强烈建议所有要安装 OpenWRT 的人通读所有说明，了解在安装过程中什么时候容易出现问题，什么时候不要做什么操作，以及一旦挂了如何处理，毕竟不是所有人的人品都很好的 :P 另外就是刷 OpenWRT 会失去保修，如果刷死了，又没有串口线之类设备能通过 tftp 刷回来的话，找商家换的时候千万不要说是自己刷死的。。

OpenWRT 的安装非常之简单，从 <http://downloads.openwrt.org/whiterussian/newest/bin/> 下载来适合你路由器的版本，每种路由器都有 squashfs 和 jffs2 两种版本，前者的文件系统是只读的，后者文件系统可写，但是稍微浪费空间一些，个人推荐第一种，官方也推荐这个，不用担心只读文件系统导致不能装其他软件，OpenWRT 使用了一些 trick 来使得 squashfs 版本够安装。安装过程和普通升级 firmware 过程一样，在 linksys 的 firmware 里面找到升级页面，直接选择下载来的 firmware，选择升级就可以了。当然，过程中不要关闭电源。升级完了以后路由器会自动重启。

OpenWRT 的第一次重启会用比较长时间，大概需要几分钟，启动过程中 OpenWRT 会点亮路由器的 DMZ 灯，灯灭了以后应该就可以连接了。默认的 IP 是 192.168.1.1, 你需要把你的网卡 IP 强行指定到 192.168.1 网段，注意如果 DMZ 灯灭了还是 ping 不通，尝试把网卡禁用一下再启用，可能跟 OpenWRT 内部使用了 VLAN 有关。

OpenWRT 安装好了以后是没有默认密码的，需要用 telnet 连上去设置初始密码。

> telnet 192.168.1.1
>
> Trying 192.168.1.1...  
> Connected to 192.168.1.1.  
> Escape character is '^]'.  
> === IMPORTANT ============================  
> Use 'passwd' to set your login password  
> this will disable telnet and enable SSH  
> \---\---\---\---\---\---\---\---\---\---\---\---\---\---
>
> BusyBox v1.00 (2006.03.24-09:16+0000) Built-in shell (ash)  
> Enter 'help' for a list of built-in commands.
>
> \_**\_**\_ **\_**\_\__ \_\_  
> | |.\-----.\-----.\-----.| | | |.\----.| |_  
> | - || _ | -_\_| || | | || \_|| \_|  
> |\_**_\_\\_\_|| \_\_|**\_\\_\_|\_\_|\_\_||\_\_\_\_\_\_\\_\_||\_\_| |\_**\_|  
> |**| W I R E L E S S F R E E D O M  
> WHITE RUSSIAN \---\---\---\---\---\---\---\---\---\---\---\----
>
> - 2 oz Vodka Mix the Vodka and Kahlua together
> - 1 oz Kahlua over ice, then float the cream or
> - 1/2oz cream milk on the top.  
>   \---\---\---\---\---\---\---\---\---\---\---\---\---\---\---\---\---  
>   root@OpenWrt:~# passwd  
>   Changing password for root  
>   Enter the new password (minimum of 5, maximum of 8 characters)  
>   Please use a combination of upper and lower case letters and numbers.  
>   Enter new password:  
>   Re-enter new password:  
>   Password changed.  
>   root@OpenWrt:~#

设置初始密码以后，就无法再 telnet OpenWRT 了，以后只能用 ssh 登录，第一次设置密码以后，OpenWRT 会启动 sshd, 这之间可能需要几分钟来生成 ssh 的 key, 路由器的 cpu 很慢，所以连不上不要着急，更不要重启。

ssh 登录进去以后可以看到这个系统上面有无数网络接口

> root@comman:~# ifconfig  
> br0 Link encap:Ethernet HWaddr 00:06:25:DA:1E:AD  
> inet addr:192.168.1.1 Bcast:192.168.1.255 Mask:255.255.255.0  
> UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1  
> RX packets:1895257 errors:0 dropped:0 overruns:0 frame:0  
> TX packets:1715280 errors:0 dropped:0 overruns:0 carrier:0  
> collisions:0 txqueuelen:0  
> RX bytes:882547891 (841.6 MiB) TX bytes:1190856407 (1.1 GiB)
>
> eth0 Link encap:Ethernet HWaddr 00:06:25:DF:90:81  
> UP BROADCAST RUNNING PROMISC MULTICAST MTU:1500 Metric:1  
> RX packets:3695441 errors:0 dropped:0 overruns:0 frame:0  
> TX packets:3674824 errors:0 dropped:0 overruns:0 carrier:0  
> collisions:0 txqueuelen:1000  
> RX bytes:2216675196 (2.0 GiB) TX bytes:2141503118 (1.9 GiB)  
> Interrupt:3
>
> eth2 Link encap:Ethernet HWaddr 00:06:25:DA:1E:AD  
> UP BROADCAST RUNNING ALLMULTI MULTICAST MTU:1500 Metric:1  
> RX packets:50309 errors:0 dropped:0 overruns:0 frame:15659  
> TX packets:69983 errors:33 dropped:0 overruns:0 carrier:0  
> collisions:0 txqueuelen:1000  
> RX bytes:6256618 (5.9 MiB) TX bytes:77584248 (73.9 MiB)  
> Interrupt:6 Base address:0x2000
>
> lo Link encap:Local Loopback  
> inet addr:127.0.0.1 Mask:255.0.0.0  
> UP LOOPBACK RUNNING MTU:16436 Metric:1  
> RX packets:16 errors:0 dropped:0 overruns:0 frame:0  
> TX packets:16 errors:0 dropped:0 overruns:0 carrier:0  
> collisions:0 txqueuelen:0  
> RX bytes:1063 (1.0 KiB) TX bytes:1063 (1.0 KiB)
>
> vlan1 Link encap:Ethernet HWaddr 00:06:25:DF:90:81  
> UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1  
> RX packets:1743209 errors:0 dropped:0 overruns:0 frame:0  
> TX packets:1920871 errors:0 dropped:0 overruns:0 carrier:0  
> collisions:0 txqueuelen:0  
> RX bytes:1190884315 (1.1 GiB) TX bytes:927881045 (884.8 MiB)
>
> vlan2 Link encap:Ethernet HWaddr 00:06:25:DF:90:81  
> UP BROADCAST RUNNING ALLMULTI MULTICAST MTU:1500 Metric:1  
> RX packets:1951915 errors:0 dropped:0 overruns:0 frame:0  
> TX packets:1753960 errors:0 dropped:0 overruns:0 carrier:0  
> collisions:0 txqueuelen:0  
> RX bytes:959278641 (914.8 MiB) TX bytes:1196757129 (1.1 GiB)

这个系统里面 eth0 是有线接口 , eth2 是无线 lan，他为了使得无线和有线 lan 都可以访问 192.168.1.1 ，把 eth0 和 eth2 加到 br0 上面，然后给 br0 绑定了 192.168.1.1 。有线接口的 lan 和 wan 是通过 vlan 来划分的。

进程不多

> PID Uid VmSize Stat Command  
> 1 root 360 S init  
> 2 root SW [keventd]  
> 3 root RWN [ksoftirqd_CPU0]  
> 4 root SW [kswapd]  
> 5 root SW [bdflush]  
> 6 root SW [kupdated]  
> 9 root SW [mtdblockd]  
> 51 root SWN [jffs2\_gcd\_mtd4]  
> 73 root 348 S logger -s -p 6 -t  
> 74 root 344 S syslogd -C 16  
> 76 root 304 S klogd  
> 371 root 408 S /bin/sh /sbin/ifup.pppoe wan  
> 387 root 592 S /usr/sbin/pppd nodetach plugin rp-pppoe.so connect /bin/true usepeerdns defaultroute replacedefaultroute  
> 488 nobody 424 S dnsmasq -K -F 192.168.1.2,192.168.1.24,255.255.255.0,12h -I ppp0  
> 493 root 400 S /usr/sbin/dropbear  
> 494 root 380 S httpd -p 80 -h /www -r Internal  
> 501 root 436 S upnp -D -L br0 -W ppp0  
> 508 root 344 S crond -c /etc/crontabs  
> 5787 root 600 R /usr/sbin/dropbear  
> 5788 root 440 S -ash  
> 6120 root 352 S /sbin/wifi  
> 6136 root 440 S /usr/sbin/nas -P /var/run/nas.lan.pid -l br0 -H 34954 -i eth2  
> 6189 root 360 R ps axf

磁盘可用情况

> Filesystem 1k-blocks Used Available Use% Mounted on  
> /dev/root 1024 1024 0 100% /rom  
> /dev/mtdblock/4 2240 1704 536 76% /  
> none 7172 40 7132 1% /tmp

/rom 就是 squashfs, 不可写, / 是可写的 jffs2 , 大部分的东西都是 symlink 到 /rom 下面去的，如果你想修改什么东西，就删除 symlink , 复制一份过来自己改改。

我的这台 CPU 主频是 125M 的, 内存 16M

> CPU: BCM4710 rev 0 at 125 MHz  
> Using 62.400 MHz high precision timer.  
> !unable to setup serial console!  
> Calibrating delay loop... 82.94 BogoMIPS  
> Memory: 14268k/16384k available (1412k kernel code, 2116k reserved, 100k data, 80k init, 0k highmem)

另外有 ipkg 类似 apt-get , 可以 ipkg install <packagename> 来安装软件包。看了一下甚至有 openvpn 之类冬冬。

Web 界面，功能不太多，基本够用，当然还是 shell 更爽一些。

[![OpenWRT webadmin][3]][4]

最后说一下使用感受，linksys 的性能确实比 TP-Link 好的不是一点半点，BT 开很多链接也不会影响上 bbs, TP-Link 的我没有拆开看，不过估计那个 cpu 也就几十 Mhz 甚至十几 Mhz 的，也太节省成本了。OpenWRT 用上以后，定制性也不成问题，实在是最理想的无线路由了，虽然价格贵一些，但是绝对超值。现在我 scrt 基本不用担心断线了。

[1]: http://openwrt.org/
[2]: http://wiki.openwrt.org/TableOfHardware
[3]: http://kangkang.org/wordpress/wp-content/uploads/2006/04/openwrt-tb.PNG
[4]: http://kangkang.org/wordpress/wp-content/uploads/2006/04/openwrt.PNG
