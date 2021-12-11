---
title: 手机做 GPRS Modem 拨号的几个要点
author: kxn
type: post
date: 2006-05-19T14:55:01+00:00
url: /index.php/archives/126
views:
  - 4292
categories:
  - Software
  - Tech Notes

---
[本周比较忙，就不更新 fterm 了，抱歉啊。。]

在 Windows 下面通常都很简单，安装对应软件和驱动，直接拨号即可，而且通常可以比较简单的切换 APN ，我这里就不废话了。

Linux 下面我一般用的是 wvdial，这个东西比直接写 pppd script 要简单很多很多。

以下几个是用手机拨号时候要注意的问题。

1: 对于 GPRS 拨号来说，wvdial 需要用 Stupid Mode, 见到 ppp 信号直接启动 pppd 。方法是在 wvdial.conf 里面对应的配置段写 Stupid Mode = yes

2: 不知道是 pppd 的问题还是 wvdial 的问题，很多手机例如 Moto E680i 在 pppd 启动以后无法获得 IP ，ppp0 接口一直都不 up。 解决方法是手动在 /etc/ppp/options 里面指定对端的 IP ，

> :10.64.64.64

10.64.64.64 是中国移动拨号的 ppp 对端 IP 。

3: 拨号成功以后，访问很多网站的时候长时间不出结果，随后断掉。这个是因为 MTU 太大，在过中国移动的 NAT 时候被丢掉了，解决方法是在 /etc/ppp/options 里面设置一个比较小的 MTU, 我随便用的是 500，没有试验最大可以到多少。

> mtu 500

4: 切换 GPRS APN 的标准拨号命令是 AT+CGDCONT=1,"IP","%APNNAME%",0,0 不过很多手机都是不认的。比较保险的方法是，在手机的网络配置那里手动创建两个配置，一个 CMNET 一个 CMWAP，记住它们在所有配置文件里面的位置，然后在拨号时候用 \*99\***N# 来选择， N 就是拨号配置的序号。手机内置的拨号配置通常是 1 和 2 ，不过我发现有些手机的默认拨号配置有问题，因此还是推荐自己创建两个使用。