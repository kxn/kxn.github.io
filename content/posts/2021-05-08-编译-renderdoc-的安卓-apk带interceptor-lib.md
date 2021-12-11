---
title: 编译 RenderDoc 的安卓 apk(带interceptor-lib)
author: kxn
type: post
date: 2021-05-08T13:23:37+00:00
url: /index.php/archives/541
categories:
  - Tech Notes

---
之前做过一次结果没有做笔记，今天又想用了，发现当时的结果全都删了，浪费了一些时间。在这里做个笔记

RenderDoc 的安卓 APK 编译严格讲没啥难的，就是啰嗦。而且如果需要好的 hook 能力的话，需要编译 interceptor-lib，这个东西需要一个非常老版本的 llvm，编译这个 llvm 也非常啰嗦。

虽然理论上讲可以在 windows 上用 MSYS 之类的编译，但是为了避免啰嗦，我们还是老老实实用 Linux 吧，反正现在的 Windows 10 都能用 WSL 了。

首先按照 <https://github.com/baldurk/renderdoc/blob/v1.x/docs/CONTRIBUTING/Compiling.md> 里面描述的，设置好 JDK/安卓SDK/安卓NDK

然后参考 <https://github.com/baldurk/renderdoc/blob/v1.x/renderdoc/3rdparty/interceptor-lib/README.md> 这篇里面的内容，先 git clone 出来，删掉 LLVMHello 相关的行。

下面重点来了，原来文章中里面有个小小的错误，在 cmake 后面的参数里面，比较新版本的 cmake 是自己选择安卓 NDK 里面的 Toolchain 的， cmake 后面参数里面加的 **-DANDROID_TOOLCHAIN=clang** 实际没有效果，导致总会选中实际不能用的 gcc toolchain，换成 **-DCMAKE\_ANDROID\_NDK\_TOOLCHAIN\_VERSION=clang** 即可，具体可以参见 /usr/share/cmake-x.xx/Modules/Platform/Android/Determine-Compiler-NDK.cmake。

其他内容文章中都是正确的，照着做即可，就能编译出来强 hook 能力的 renderdoc 安卓 apk 了。