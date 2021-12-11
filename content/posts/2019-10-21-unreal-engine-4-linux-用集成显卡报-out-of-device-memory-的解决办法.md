---
title: Unreal Engine 4 Linux 用集成显卡报 out of device memory 的解决办法
author: kxn
type: post
date: 2019-10-21T04:39:39+00:00
url: /index.php/archives/393
categories:
  - Tech Notes

---
UE4 在 Linux 下面默认用 Vulkan 作为渲染 API，但是 Vulkan 渲染代码在遇到显存不够的时候会直接退出，如果用集成显卡，或者很老的独立显卡运行　UE4 的话，那么先编辑

Engine/Config/BaseEngine.ini

取消下面这行的注释

; OpenGL4 is deprecated, you can comment this back in to add it to your targeted RHI list  
+TargetedRHIs=GLSL_430

然后把前面这行注释掉  
;+TargetedRHIs=SF\_VULKAN\_SM5

就能启动了，启动时候会报个 OpenGL　已经废弃的警告，忽略就行。