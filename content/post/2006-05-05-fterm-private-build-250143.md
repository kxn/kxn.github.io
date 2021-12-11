---
title: FTerm private build 2.5.0.143
author: kxn
type: post
date: 2006-05-05T06:30:33+00:00
url: /post/120.html
views:
  - 2621
categories:
  - Tech Notes
---

ChangeLog:

- 修正 ssh 在用户没有密码时的状态机错误，现在可以用来上 ptt 了(mephisto 报告)
- 修正普通 User 第一次运行注册 TLB 不成功会崩溃的问题，但是不知道在完全没有运行过 fterm 的电脑上面是否能正常工作(diode 报告)
- 修正点击链接时候识别经常不对的问题 (timely 报告)
- 修正 IP 显示框在失去焦点时候不消失的问题 (timely 报告)
- 修正了 IP 背景框颜色跟随系统主题导致看不清楚的问题(timely 报告)
- 继续翻译了一部分界面
  已知问题：  
  bbs 上面的 Zmodem 现在不可用。

欢迎[报告 bug][1]

[教育网下载][2]  
[网通电信海外下载][3]  
[备用下载][4]

[1]: http://kangkang.org/wordpress/index.php/report-fterm-bugs/
[2]: http://comman.org/fterm/files/fterm-current.rar
[3]: http://cache.kangkang.org/fterm/files/fterm-current.rar
[4]: http://kangkang.org/fterm/files/fterm-current.rar
