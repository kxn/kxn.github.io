---
title: 用 icc ipo 编译 squid
author: kxn
type: post
date: 2005-12-13T09:58:06+00:00
url: /post/38.html
views:
  - 2546
categories:
  - Network
  - Tech Notes
---

其实就是要把 Makefile 里面的 ar 换成 xiar，这不是一个很难的事情，但是 squid 不知道用的什么版本的 autotools, 如果重新生成 Makefile 的话，会有很多乱七八糟的错误，而且还编译不过，因此最简单的办法是直接在 configure 之后，替换所有 ar 为 xiar 就好了

configure 参数我用的这个

CC=icc CXX=icc CFLAGS="-O3 -ipo -axKWP -funroll-loops -unroll256 -mcpu=pentium4 -mtune=pentium4 -march=pentium4 -fomit-frame-pointer -static-libcxa -cxxlib-icc -i-static -fno-exceptions -no-prec-div" ./configure --enable-epoll --prefix=/var/test/squid --enable-storeio="aufs,ufs,diskd" --with-aufs-threads=3 --with-pthreads --enable-removal-policies=heap,lru --disable-delay-pools --enable-underscores --disable-ident-lookups --enable-time-hack --enable-cache-digests --disable-forw-via-db

然后简单替换所有 Makefile 里面的 /usr/bin/ar 为 xiar , 这里本来应该用 AC_SUBST(AR) 的，但是重新生成 Makefile 有问题，又懒得看，sigh

find -name "Makefile" -exec sh -c "cat "{}"| sed -e 's/\/usr\/bin\/ar/xiar/g' > "{}".tmp ; mv "{}".tmp "{} \;

然后就可以带 ipo 编译了
