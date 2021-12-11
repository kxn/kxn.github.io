---
title: Golang socket 里面奇怪的 pipe 使用
author: kxn
type: post
date: 2021-01-21T06:06:46+00:00
url: /post/509.html
categories:
  - Tech Notes
---

有个 golang 写的 proxy server, 大概日常 tcp 连接数两万多左右，结果某次在 /proc/pid/fd 下面一看，竟然有五到六万多文件，ls -l 一看，居然 socket 只有两万多，pipe 反而有三万多小四万，这就奇了怪了，哪儿来的这么多 pipe？

读了一阵子源码搞清楚了，golang 在 Linux 下面，对于 fd 对拷这种操作【具体是 net.Conn.readFrom(net.Conn)】，有个用 splice 系统调用的优化，会先试着用 splice 调用在内核里面完成两个 fd 之间的拷贝，这个可以省去将大量数据从 kernel 里面拷贝到 user space 的操作。如果失败才正常拷贝。

然而 splice 这个调用可能是为了内部实现方便，目标一方必须是 pipe 类型的 fd。所以 golang 里面的实现其实比较龌龊，先创建一对 pipe fd，然后从源 fd splice 进 pipe 入端，接下来从 pipe 出端 splice 到目标 fd。 这样下来一个 readFrom 操作，里面要一次 pipe2 调用，2 次 splice 调用，还有 2 次 close 操作关掉 pipe。有趣的是，即使这么龌龊的实现，在数据量大的应用，比如 proxy 上面，依然可以达到比直接拷贝更好的性能，可以参考下面的第一个链接。

唯一感觉比较不爽的也就是每次 readFrom 都要创建 pipe 再销毁了，这个为了不影响 golang 的架构设计，可能也只能这么做。不过一般 proxy 里面最后大多是 io.Copy ，而这个调用一旦调用就直到 tcp 连接断开了，生命周期不会太短，所以完全没有必要再做任何优化了。

参考传送门

<https://github.com/golang/go/issues/10948>  
<https://github.com/golang/go/blob/0e85fd7561de869add933801c531bf25dee9561c/src/net/tcpsock_posix.go#L47>  
<https://man7.org/linux/man-pages/man2/splice.2.html>
