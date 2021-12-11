---
title: 举报垃圾短信的 android app
author: kxn
type: post
date: 2013-06-16T15:26:45+00:00
url: /index.php/archives/272
categories:
  - Tech Notes

---
今天有点时间，照着网上例子边学边写，做了人生第一个 android app。

功能很简单，就是列出所有短信，然后可以选择举报，程序会把垃圾号码加 * 然后和垃圾内容合一起，砍掉超过 70 字的部分，然后发送给 10086999。发送短信部分不是直接发的而是很土鳖的调用系统短信程序，还得手动再按下发送。原因是MTK双卡机上直接发默认用的是第一个卡，而我移动卡是第二个卡，看了一下双卡机接口，发现很折腾，决定算了，多按一下按钮也不算啥。。。

友情提醒，只有移动是有意义而且不花短信费的！联通电信啥的就不要用了，没意义。

[下载][1]  
[源代码下载][2](请勿嘲笑。。。) 

以前我记得在网上找过类似程序，只有一个，在我双卡机上还没法用。发了微薄以后有不少网友指出有些应用也有这个功能。确实以前没发现过。

 [1]: http://blog.kangkang.org/wp-content/uploads/JuBaoDuanXin.apk "JuBaoDuanXin.apk"
 [2]: http://blog.kangkang.org/wp-content/uploads/JuBaoDuanXin.tar.gz "JuBaoDuanXin.tar.gz"