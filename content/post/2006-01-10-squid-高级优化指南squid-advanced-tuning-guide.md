---
title: Squid 高级优化指南/Squid Advanced Tuning Guide
author: kxn
type: post
date: 2006-01-10T04:04:25+00:00
url: /post/57.html
views:
  - 5950
categories:
  - Network
  - Tech Notes
---

Squid 高级优化指南

by kang[at]kangkang[dot]org , 转载请保留

类似的题目网上已经有很多了，为啥我还要写这么一篇？其实是前段时间接手了一个 squid 优化的事情，在网上搜索了一下，发现很多 squid 优化只限于在 squid 参数和系统参数上面的调整。但是这个实在只是细枝末节的事情，只要不是太弱智的配置导致无法缓存，squid 的性能不会有太大差距，也就提高 10%左右，只有实际的业务针对 squid 进行一些调整，squid 才会真正爆发出他的能量，很多时候有 100%-200% 的提升。

本文基本是一些方向性的指导，并不涉及像具体配置文件的细节，因此本文里面的内容大部分不能往配置文件里面 copy-paste。。

首先要明确一下，squid 能够用来作什么。很多人没有搞明白 squid 的工作原理，只是听说 squid 性能不错可以用来给网站提速，就直接在自己的 website 前面套了一个 squid ，这基本没有任何用处，即使你都是静态页面，后面 apache 上面没有开 mod_expires，一样缓存不了，squid 只能起到一个连接管理的用处。

一般说来，网站用 squid 加速，目的有二

1:  squid 本身具有缓存功能，可以将 webserver 输出的内容缓存起来，在缓存没有过期之前来的访问，都直接用缓存里面的内容，这样可以有效减少 webserver 机器上面的请求数量。这是 squid 的主要功用。  
2: 网络慢的用户会长时间占用 webserver 的 TCP 连接，webserver 对每个连接占用的资源比较大，如果长时间不能释放出来服务其他请求，性能会有比较大的影响。前面放一个 squid, webserver 就可以迅速处理完逻辑以后，把数据快速发送给 squid, 然后去处理别的逻辑，而 squid 每个 TCP 连接占用的资源很少，不用担心占用太多资源。这个用途也叫做连接管理，有一些网络设备也可以做这个事情，价格都很贵。

下面针对 squid 的两种功用，来讲述如何调整业务逻辑和 squid 参数

零：预操作

在搞 squid 之前，不管你用什么编译配置，需要什么特殊选项，都请 --enable-snmp ，并配置好 mrtg 之类，可以图形化的显示 squid 状态，例如 Request Hit Ratio(RHR), Byte Hit Ratio(BHR), 等等，反馈是做一切事情的基础，优化也不例外。

一：缓存

A: 使用 Expires header  来控制缓存

squid 在缓存 webserver 内容的时候，需要后端 webserver 输出一些控制信息告诉他页面是不是可以被缓存，以及可以缓存多久。否则 squid 是不会自作主张给你缓存内容的。一个页面到底能不能缓存，只有开发网站的人才知道，因此开发人员有责任在动态页面里面输出 Expires 和 Cache-Control header。简单举一个 php 的例子以说明这两个 header 的值是什么含义，其中$expiretime 的单位是秒。

header("Expires: " . gmt_date_format(time()+$expiretime));  
 header("Cache-Control: max-age=" . "$expiretime");

对于静态文件，有两种方式来让 squid 自动给静态文件缓存，一种是使用 apache 的 mod_expires ，可以针对路径或者针对文件类型/扩展名来自动输出 cache 头。详细的请参考 [mod_expires 的说明][1] 。另一种是用 squid 的 refresh_pattern 来指定。详细的还是请参考 [squid 的配置文件][2]。一般来说，如果后端不是配置很麻烦，建议还是在后端做，前端的配置修改大多数都是违背 http 协议的，如果出现问题，也比较难排查。

(待续)

最后致谢一下 [windtear][3] ，这位是 squid 之王, Lord of Squid, 业务分拆是从他那里学来的。致敬。(假如有人转载请不要删除这个，否则你也太没良心了)

Update: 修改了一点文字和排版

[1]: http://httpd.apache.org/docs/2.0/mod/mod_expires.html "Apache mod_expires"
[2]: ttp://squid.visolve.com/squid/index.htm "Squid Configuration Guide"
[3]: http://windtear.net "Windtear"
