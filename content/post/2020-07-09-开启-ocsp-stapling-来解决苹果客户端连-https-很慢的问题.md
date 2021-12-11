---
title: 开启 OCSP Stapling 来解决苹果客户端连 https 很慢的问题
author: kxn
type: post
date: 2020-07-09T12:59:21+00:00
url: /post/454.html
categories:
  - Tech Notes
---

最近墙不知道为何抽风把 ocsp.int-x3.letsencrypt.org 给封了，导致所有用 letsencrypt 证书的网站，用 iOS 客户端连的时候都要卡几秒验证 ocsp。 这个破 blog 当然也受到了影响。

解决还是很容易的，打开 OCSP Stapling 就可以了。这个原理简单说，就是让本来该客户端去验证 ocsp 的事情，由 nginx 替它做了，去下来 ocsp 的结果然后和证书一起发给客户端，客户端验证以后就不会再去访问了。

参考文章 [https://blog.fundebug.com/2018/03/07/nginx_ocsp_stapling/][1]

当然需要注意的是，这个破 blog 位于香港，所以 nginx 不受墙影响可以直接拿到 ocsp 的结果。如果你的服务器位于墙内，那你需要自己解决你的服务器访问 ocsp.int-x3.letsencrypt.org 的问题。

弄好了以后，可以用 <https://www.ssllabs.com/ssltest/analyze.html> 这个页面来验证一下是否已经成功开启 OCSP Stapling。

[1]: https://blog.fundebug.com/2018/03/07/nginx_ocsp_stapling/
