---
title: 用 JTAG 修复变砖的 DELL TrueMobile 2300 路由器
author: kxn
type: post
date: 2008-02-10T08:45:27+00:00
url: /post/183.html
views:
  - 3937
categories:
  - Tech Notes
tags:
  - jtag debrick dell 2300 无线路由器 修复 openwrt
---

[本来应该早写这篇 blog 的，后来忙于别的事情，就放下了，现在算是补交作业吧]  
[本文发布于 blog.kangkang.org，转载请勿删除]

前段时间[自己的 Linksys WRT54G 电源坏掉][1]，不得已在淘宝上面寻找替代品，最后找到了 Dell TrueMobile 2300 这个好东西，这个大概是满足 OpenWRT 最低要求(4M flash 16M ram) 的无线路由器里面最便宜的了(16x-18x) 。于是买了一个回来，用了没多久，就[在一次刷 tomato 的过程中给刷死了][2]，开机所有网口指示灯亮黄灯，电脑网卡显示网络电缆被拔出，彻底变成了一块砖头。

在网上搜索了半天，试验了各种诸如短接 flash 芯片引脚之类的办法，没有一个好用的。最后还是拿出电烙铁，按照网上文章焊了一个简易 JTAG 线，把这个 Dell 无线路由器修复。

> 小知识: 什么是 JTAG 以及为什么可以用 JTAG 来修复变砖的电子设备  
> 网上搜索 JTAG，通常能找到的叫法是 Joint Test Action Group，是个规范。为啥能用来修复电子设备则很少有人讲清楚。简单的说，JTAG 就是一个芯片的后门规范，通过简单的电气接口可以命令芯片做任何它应该能做事情，包括运行任何指令。这个接口本来是为了测试芯片方便的，当然对于 firmware 损坏的电子设备，可以通过命令 CPU 执行指令来刷写 flash，从而达到修复变砖电子设备的目的。

拆开 Dell 2300, 可以看到主板如下图（盗用了恩山论坛某哥们的图，我自己的照得没他清楚）

<img src='http://blog.kangkang.org/wordpress/wp-content/uploads/2008/02/dell2300.png' width=540 height=405 alt='Dell 2300' />

主板上面有两个地方有现成的接口，其中右边那个 12 针一般就是 WRT 系列路由器的 JTAG 接口了，为了方便重复使用，我从一个破主板上面拆了个插座焊上去。我们只需要前面 10 针就够了，因此我这里最右边的两个针没焊。

<img src='http://blog.kangkang.org/wordpress/wp-content/uploads/2008/02/img\_0021a.jpg' width=540 height=405 alt='img\_0021a.jpg' />

做完了接口还需要做线。电路和接口线序可以参考 [这里][3]。在 Dell 主板上面插头的左下方有个小白三角，这个表示第一个针脚。

JTAG 线有两种，一种叫做有缓冲的(buffered)，一种叫做无缓冲的(unbuffered)。简单的说 Buffered JTAG 就是里面有个电子电路能帮你存储电脑发过来的 JTAG 命令，即使中间信号不是很稳定，电子电路也能正确处理。Unbuffered 就是直接接到芯片上面，我们自己做没有合用的电子元件，因此做的就是 unbuffered，这种情况下面信号抗干扰能力比较差，网上说这样的线长度一般不要超过 15cm。

我用的是一个废弃的并口打印机线，和某废弃主板带的一个后置 USB 接口线。我手边没有电阻，但是看电路板上面似乎已经有电阻了，于是干脆没接，实际证明也确实能用。  
<img src='http://blog.kangkang.org/wordpress/wp-content/uploads/2008/02/img\_0015a.jpg' width=540 height=405 alt='img\_0015a.jpg' />

全都弄好以后就可以开始刷写 flash 了。  
<img src='http://blog.kangkang.org/wordpress/wp-content/uploads/2008/02/img\_0013\_b.jpg' width=540 height=405 alt='img\_0013\_b.jpg' />

前面提到过，JTAG 刷写 flash 的原理就是让 CPU 执行指令来刷写 flash, 而各家 flash 厂商刷写的方式经常不一样，因此用来刷 flash 的 JTAG 工具也会随着不同的 flash 芯片和 CPU 本身的不同而变化，网上流传的 wrt debrick utillity 里面集成了很多常见 CPU 类型和 flash 类型，但是恰好没有能支持 Dell 2300 v2 里面用的这片 flash 的。好在[恩山论坛的 qwea 网友修改了一个版本][4]，可以直接用来刷 Dell 2300， 在此向他表示感谢。

刷写 flash 并不是像电脑刷 BIOS 那样，随便抓一个文件来刷上就成了。很多时候我们手边并没有可用来刷的文件，而且还要小心别刷坏了，原因见下分解。

基于 Broadcom 解决方案的 WRT 系列路由器里面， flash 通常被分为三部分， CFE, KERNEL, NVRAM。 CFE(Common Firmware Environment) 大概类似电脑的 BIOS，负责读取 NVRAM 里面的配置，初始化各种硬件，然后引导 kernel 部分。 kernel 就是真正干活的 firmware 部分，一般刷机刷的都是这部分。CFE 在启动时候会检测一下 kernel 部分的校验和，如果发现是坏的，CFE 会停下来等待人从网络上面 tftp 传送过来新的 kernel 镜像并启动。因此一般 WRT 系列路由器并不容易刷死，但是有些机型像 Dell 的这个，CFE 有 bug, 如果他启动时候发现 NVRAM 的内容有问题，他就停在那里不动了，也不初始化硬件，因此对外表现就是连网口都不通。在这种情况下面，我们只需要用 JTAG 工具将坏的 NVRAM 部分清空， CFE 在启动时候会自动将备份的 NVRAM 拷贝过去，然后等待 tftp 刷新。如果上来就冒冒失失的将整个 flash 芯片包括 CFE 全部清空，就麻烦了，只能到网络上面寻找别人备份出来的完整 flash 刷回去。

清空 flash 以后，路由器的 CFE 会自动恢复 NVRAM，并等待 tftp 刷新，找一个 OpenWrt 或者 DD-WRT 的 mini 版本 tftp 刷进去就可以了( tftp 不能用 tomato 之类大小大于 3M 的 flash，可以刷好了以后再用网页方式刷)。

本文内容很多来自[恩山 WiFi 论坛][5]，在此致谢。  
最后祝 RoachCock@newsmth 早日修复他手里两个变砖的 Dell 2300。

遗留问题：在 Dell 2300 主板下方有个 9 针插口，不知道这个是串口还是 USB 接口呢？

[1]: http://kxn.spaces.live.com/blog/cns!626140570A4EA541!1000.entry
[2]: http://kxn.spaces.live.com/blog/cns!626140570A4EA541!1083.entry
[3]: http://wiki.openwrt.org/OpenWrtDocs/Customizing/Hardware/JTAG_Cable
[4]: http://www.right.com.cn/forum/viewthread.php?tid=12838
[5]: http://www.right.com.cn/forum/
