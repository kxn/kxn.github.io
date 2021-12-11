---
title: 修改Windows TCP参数来对付北京网通垃圾的ADSL
author: kxn
type: post
date: 2006-05-28T14:14:01+00:00
url: /index.php/archives/129
views:
  - 4766
categories:
  - Network
  - Tech Notes

---
最近北京网通的 ADSL 实在快不能忍了，到晚上就丢包率过 10% ，上个 bbs 卡得不行，于是 google 了一通，修改了几个Windows TCP的参数，以浪费带宽为代价提高响应速度（比起下载和上 bbs 来，我还是更重视 bbs 速度，我想网游一族应该也是这样）。效果似乎还可以，不知道是心理作用还是怎么回事。不过确实见不到在 bbs 上面一卡半天的现象了。

这些修改都可以在 <http://www.winguides.com/registry/category.php?310> 找到，针对不同系统的区别也有介绍，下面只在 Windows 2003 上面测试通过，其他系统可能不是在这个注册表位置，或者根本就不支持。

所有的修改都位于 HKEY\_LOCAL\_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters 下面，大部分都在注册表里面默认没有，需要手工添加，都是 DWORD 类型的值。

1: InitialRtt 

这个是 Windows 对于一个 TCP 连接的初始重传超时。单位毫秒，改成 1000 ，你要改成 500 估计也问题不大。 

2: TcpMaxDupAcks 默认 2 ， 改成 1， 单位是个，

这个参数控制 TCP 快速重传，TCP 通常是在重传定时器到时的时候才重新传输某个帧，虽然重传定时器是自适应的，但是如果还算通畅的网络上面忽然丢一个，比如北京网通 ADSL 这样的，上 BBS 将会明显感到卡了一下。快速重传对这种情形很有效，它是如果看到对方发了好几个相同的 ACK 过来，就知道对方一直在等同一个包，于是 TCP 协议栈会马上重传这个包。上面提到的这个参数就是控制 Windows 看到几个重复的 ACK 时候就进行快速重传，默认是 2 ，实际指的是看到三个序列相同的 ACK。 我改成 1 。

其他还有不少 TCP 调节选项，但是对这个丢包影响不是很大了。所以不在这里介绍。

修改完以后需要重启一下机器生效。