---
title: MySQL 的 filesort 不是 stable 的
author: kxn
type: post
date: 2008-09-01T14:27:44+00:00
url: /index.php/archives/205
views:
  - 558
categories:
  - Tech Notes
tags:
  - filesort
  - mysql
  - unstable

---
呃，别会错意，我的意思不是说 MySQL 开始 filesort 就会不稳定，而是说他的排序算法不是 stable 的。

某表中有两个字段，一个是 log\_time, DATETIME 类型， 一个是 user\_id , BIGINT 类型。 SQL 是 SELECT XXX FROM xx\_table WHERE user\_id = 111 ORDER BY log_time DESC 。

一开始表上的索引建错了，建成了 user\_id,log\_time 的联合索引，于是上面的 SQL 用上这个索引以后，就做了 filesort ， 这时候发现所有 log\_time 相同的列顺序是乱的，有的按照插入顺序，有的不是。而当重新建立 log\_time,user\_id 的索引之后，结果中 log\_time 相同的就都是按照插入顺序来排序的了。

这个发现有啥用？似乎也没啥太大用处倒是。如果为了严格保持插入顺序，那么设计时候就应该用精度更高的 log_time 。不过如果对插入顺序的要求不是非常严格的要求，那么正确建立索引直接利用它的特性也还算省事。