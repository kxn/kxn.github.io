---
title: 修正 RSS feed 的 bug.
author: kxn
type: post
date: 2006-03-24T14:22:42+00:00
url: /index.php/archives/80
views:
  - 2476
categories:
  - Tech Notes

---
今天把 WordPress 2.0.1 的 rewrite bug 修正了，这个 bug 的表现是 rss 不管选的什么，都只输出 comment 的 rss。以前经常有人说我的 rss 不正确，一直都没有上心，以为是他们的 RPWT。今天终于有人直接留言指出问题了，结果 google 了一下发现还真是一个 wp bug, 我真是好运气。。

不管如何，现在问题已经修正啦，多谢指出问题的 wisi 和提供解决方法的 [istef][1] 兄台。

 [1]: http://blog.istef.info/2006/02/08/wordpress-201-url-rewrite-bug/