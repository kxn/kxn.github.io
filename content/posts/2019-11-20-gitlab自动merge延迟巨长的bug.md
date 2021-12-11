---
title: GitLab自动merge延迟巨长的bug
author: kxn
type: post
date: 2019-11-20T05:34:27+00:00
url: /index.php/archives/418
categories:
  - Tech Notes

---
有一个 GitLab 上开了 CI，设置 pipeline 测试通过以后自动 merge，一开始还可以，到后来自动 merge 的响应速度越来越慢，经常是 pipeline 通过以后等了一天都没有什么动静。

在GitLab Admin Area 里面 Monitoring -> Background Jobs 里面查看，发现队列里面有上万的 automerge:auto\_merge\_process 任务在等待，然后看了一下细节，发现每个 Merge Request 都起了一个任务，不管是不是已经 merged，但是实际上大部分都已经 merged 了，所以这些 job 就是起来看了一下这个 MR 已经被 merged 了，就退出了。

也懒得去看 gitlab 代码了，直接执行 /opt/gitlab/bin/gitlab-pgsql， 进去以后 update merge\_requests set merge\_when\_pipeline\_succeeds = false where state = 'merged' or state = 'closed';

然后在 background jobs 里面把堆积的 job 全都删掉，问题立即就解决了。