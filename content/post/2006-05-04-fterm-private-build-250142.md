---
title: FTerm private build 2.5.0.142
author: kxn
type: post
date: 2006-05-04T13:29:50+00:00
url: /post/118.html
views:
  - 2217
categories:
  - Software
  - Tech Notes
---

ChangeLog:

- 在预览图片的时候不发送 Pragma: no-cache 头，避免对很多 bbs 站点造成压力冲击。(atppp 报告)
- 在有多个 Tab 打开并连接的情况下面，关闭整个 fterm 或者注销的时候只需要一次回答。
- Capslock 打开的时候，按 shift 输入的字符仍然是大写 (Sleepy2000 报告)
- 默认对 .png 结尾的 URL 不能自动识别。(ypdng 报告)
- 发行包里面将 IP 数据库换成了 Soff 的珊蝴虫精简版 IP 数据，大小从 4M 缩小到了 2M，伟哉 soff
- 主菜单 About 里面增加了报告 bug 项
  [教育网下载][1]  
  [网通电信海外下载][2]  
  [备用下载][3]

[1]: http://comman.org/fterm/files/fterm-current.rarr
[2]: http://cache.kangkang.org/fterm/files/fterm-current.rar
[3]: http://kangkang.org/fterm/files/fterm-current.rar
