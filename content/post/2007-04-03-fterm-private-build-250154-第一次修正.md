---
title: Fterm private build 2.5.0.154 第一次修正
author: kxn
type: post
date: 2007-04-03T13:59:01+00:00
url: /post/158.html
views:
  - 3234
categories:
  - Tech Notes
---

改了几个 154 里面引进的 bug, 看来不能深夜干活。  
版本号还是停留在 154, 只是把下载文件更新了一下而已。155 这个版本号准备留给比较大的修改。

ChangeLog

- 修改了有人报告的英文系统下面如果调出不能正常显示的界面以后，中文输入会不正常的现象
- 修改了退出预览图片窗口以后再次预览同一个图片，会显示不正确的问题。
- 改成除了 esc, 其他任意键都可以退出预览图片窗口
- 进一步提高了预览大图片的加载速度

欢迎[报告 bug][1]

除非非常严重的稳定 bug，以后不要在 bbs 上面给我写信或者直接发帖报 bug 了，我实在没有功夫一个一个倒进 bug 库。

[网通和教育网下载][2]  
[电信下载][3]

[1]: http://blog.kangkang.org/index.php/report-fterm-bugs/
[2]: http://blog.kangkang.org/fterm/files/fterm-current.rar
[3]: http://cache.kangkang.org/fterm/files/fterm-current.rar
