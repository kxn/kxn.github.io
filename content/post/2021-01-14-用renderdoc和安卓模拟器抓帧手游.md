---
title: 用RenderDoc和安卓模拟器抓帧手游
author: kxn
type: post
date: 2021-01-14T14:44:22+00:00
url: /post/504.html
categories:
  - Tech Notes
---

知乎上有一篇用 Intel GPA 和安卓模拟器抓帧手游的帖子，不过 Intel GPA 配置相对比较繁琐，还有版本问题。还是 RenderDoc 比较习惯。所以研究了一下如何用 RenderDoc 来抓帧模拟器，倒是蛮简单的。这样手游只要能用模拟器运行，就一定可以被抓帧，什么 ro.debuggable 啊，USB 调试啊都不需要管，root 也不用开，绝无被检测到的可能。

首先要设置 Windows 环境变量 `RENDERDOC_HOOK_EGL = 0` ，这个是为了防止 RenderDoc 把模拟器里面实现的 GLES API 给 Hook 了的同时还 Hook 了 DirectX 造成冲突。设置的时候需要右键点击我的电脑（Win10 里面叫此电脑），选择属性里面有个修改环境变量的地方。不要试图在命令行上设置，没有用。

其次把模拟器的渲染模式改成 DirectX，原因同上。

在 RenderDoc **Tools->Settings->General** 里面找到 **Allow global process hooking** 并勾选

然后找到模拟器的核心文件，一般是一个叫 XXXHeadLess.exe 的文件，也可能是其他的。找到的方法很简单，模拟器里面随便运行一个手游，然后任务管理器里面按照 CPU 使用排序，排在最前面的就是，右键点击之，选择打开文件所在位置。就可以找到核心文件的位置。

在 RenderDoc 的 **Launch Application** 页面里面。**Executable Path** 选择刚才找到的模拟器核心。然后在下面 **Global Process Hook** 里面点 **Enable Global Hook**，如果提示需要 Administrator 启动就确定以后再点 **Enable Global Hook** 按钮。

退掉所有模拟器，（注意一定要退干净，有时候模拟器界面关掉了，核心还在后台运行。可以在任务管理器里面查看模拟器的核心是否还在运行，还在运行的话用任务管理器杀掉）然后重新启动模拟器，这时候应该能看到模拟器画面左上角已经显示 RenderDoc 的信息了，如果没有，请检查前面操作是否正确，没有 RenderDoc 的显示信息说明完全没有加载成功。

RenderDoc **File** 菜单 **Attach to Running Instance** , 在 localhost 下面可以看到模拟器核心程序，选中并点击 **Connect to app** ，之后就正常抓帧即可。

- 如果模拟器里面没有启动手游，而是停在桌面上的话，刚 Connect 上的时候有可能会显示 D3D11 Not Present，没法抓帧，不用担心，这是因为模拟器没有渲染动作。正常启动游戏就可以抓了

- 模拟器毕竟是把安卓的渲染 API 给翻译到 D3D11 API，所以确实有可能和实际渲染操作有些许差别，特别是采用多线程渲染方式的手游，抓帧下来的结果时序很有可能是不对的，这个就没有办法了，就当个补充好了。

- 我测试都是用 64 位模拟器做的，没试验 32 位的行不行

- （感谢 cx 网友指出）如果怎么倒腾都没有效果，请进入 BIOS 检查 SecureBoot 是否开启，如果开着的话，请关了以后再进入 Windows，原因是从 Windows 8 开始， RenderDoc Global Hook 依赖的机制 Appinit_dlls 当 SecureBoot 开启的时候是被禁用的。参见 <a href="https://docs.microsoft.com/en-us/windows/win32/dlls/secure-boot-and-appinit-dlls" rel="noopener" target="_blank"><a href="https://docs.microsoft.com/en-us/windows/win32/dlls/secure-boot-and-appinit-dlls">https://docs.microsoft.com/en-us/windows/win32/dlls/secure-boot-and-appinit-dlls</a></a>
