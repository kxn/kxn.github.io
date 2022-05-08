---
title: "Build Wine on Fedora 35"
date: 2022-05-08T23:26:46+08:00
author: kxn
type: post
url: /post/Build-Wine-On-Fedora-35.html
categories:
  - Tech Notes
---

最近在 Fedora 上面自己 Build 了一次 Wine，中间掉了好几次坑，在这里记录一下。

Build wine 最困难的点主要还是需要同时 build 一份 64 和 32 位共存的 wine，这要求同时安装一大票库的 64 和 32 位版本，装来装去的非常容易漏，而 Wine 的 configure 脚本并不会对所有事情都警告，有些库没有也不会说什么，然而 wine 的主要用途微信是 32 位的，所以编译出来的 wine 就各种毛病。

需要的依赖如下

dnf install -y libX11-devel.x86_64 libX11-devel.i686 flex bison mingw64-gcc mingw32-gcc libXcursor-devel.i686 libXcursor-devel.x86_64 libXi-devel.x86_64 libXi-devel.i686 libXxf86vm-devel.x86_64 libXxf86vm-devel.i686 libXrandr-devel.i686 libXrandr-devel.x86_64 libXfixes-devel.x86_64 libXfixes-devel.i686 libXinerama-devel.i686 libXinerama-devel.x86_64 libXcomposite-devel.i686 libXcomposite-devel.x86_64 mesa-libOSMesa-devel.x86_64 mesa-libOSMesa-devel.i686  ocl-icd-devel.x86_64 ocl-icd-devel.i686 libpcap-devel.i686 libpcap-devel.x86_64 dbus-devel.x86_64 dbus-devel.i686  sane-backends-devel.x86_64 sane-backends-devel.i686 libusb1-devel.i686 libusb1-devel.x86_64 libv4l-devel.i686 libv4l-devel.x86_64 libgphoto2-devel.i686 libgphoto2-devel.x86_64 pulseaudio-libs-devel.x86_64 pulseaudio-libs-devel.i686 gstreamer1-plugins-base-devel.x86_64 gstreamer1-plugins-base-devel.i686 cups-devel.x86_64 cups-devel.i686 SDL2-devel.x86_64 SDL2-devel.i686 fontconfig-devel.x86_64 fontconfig-devel.i686 samba-devel.x86_64 samba-devel.i686 openal-soft-devel.x86_64 openal-soft-devel.i686 vulkan-loader-devel.x86_64 vulkan-loader-devel.i686 libXrender-devel.x86_64 libXrender-devel.i686  gnutls-devel.x86_64 gnutls-devel.i686 libgudev-devel.x86_64 libgudev-devel.i686 freetype-devel.x86_64 freetype-devel.i686 libXext-devel.i686 libXext-devel.x86_64 glib2-devel.i686 glib2-devel.x86_64 gstreamer1-devel.i686 gstreamer1-devel.x86_64

此外编译 32 位的时候有几个问题需要注意

Wine 的文档里面明确说了，编译 32 位时候需要用 PKG_CONFIG_PATH 指定路径，文档里面说一般是 /usr/lib 或者 /usr/lib32，应该是 /usr/lib/pkgconfig ，否则不会输出正确的配置，导致 32 位无法检测到正确的 include 路径

Fedora 35 的 libgudev-devel.i686 有个 bug，需要把 /usr/lib/libudev.so.1 建立个软连接到 /usr/lib/libudev.so ，否则 32 位的脚本还是找不到它。

32位编译的时候如果没有安装 libXext-devel.i686 不会报错，但是编译出来的 wine 不支持剪贴板

