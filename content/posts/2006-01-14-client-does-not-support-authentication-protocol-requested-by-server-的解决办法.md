---
title: Client does not support authentication protocol requested by server 的解决办法
author: kxn
type: post
date: 2006-01-14T09:08:11+00:00
url: /index.php/archives/62
views:
  - 2521
categories:
  - Tech Notes

---
网上一 google 就找到了，不过 dev.mysql.com 很慢，在这里备个份。

php4/5 里面带的 mysql  扩展不支持 mysql 4.1 的新密码加密方式，但是支持的 mysqli 库的接口变化太多。

最简单的是降级 mysql 的密码加密方式

mysql> SET PASSWORD FOR 'some\_user'@'some\_host' = OLD_PASSWORD('newpwd');