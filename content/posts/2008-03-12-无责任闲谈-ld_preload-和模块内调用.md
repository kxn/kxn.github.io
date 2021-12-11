---
title: 无责任闲谈 – LD_PRELOAD 和模块内调用
author: kxn
type: post
date: 2008-03-11T08:19:07+00:00
url: /index.php/archives/192
views:
  - 1395
categories:
  - Tech Notes

---
**无责任闲谈 -** **LD_PRELOAD 和模块内调用**

这个问题的提出是因为某天伟大的布总问了我一个问题。

如果某个 .so 里面有两个函数， 一个 foo ，一个 bar， bar 调用了 foo ，那么 bar 调用 foo 的地方应该是直接生成对 foo 的调用呢？还是通过跳转表来实现呢？

我们讨论了一下，都觉得如果没有加 -fPIC 的话，这个怎么也不应该通过跳转表来实现。但是布总却发现，如果之前弄一个 inject.so 里面包含一个叫做 foo 的函数，然后通过 LD_PRELOAD 来预先加载，不管编译时候有没有加 -fPIC，调用 bar 的时候，调用的都是 inject.so 里面的 foo 函数。这就有点奇怪了，ld-linux.so 怎么会去修改原来 .so 中 foo 对 bar 的调用呢？

还是反汇编一下看看吧：

不带 -fPIC

00000498 <foo>:  
498: 55 push %ebp  
499: 89 e5 mov %esp,%ebp  
49b: 5d pop %ebp  
49c: c3 ret

0000049d <bar>:  
49d: 55 push %ebp  
49e: 89 e5 mov %esp,%ebp  
4a0: e8 fc ff ff ff call 4a1 <bar+0x4>  
4a5: 5d pop %ebp

带 -fPIC  
000004a8 <foo>:  
4a8: 55 push %ebp  
4a9: 89 e5 mov %esp,%ebp  
4ab: 5d pop %ebp  
4ac: c3 ret

000004ad <bar>:  
4ad: 55 push %ebp  
4ae: 89 e5 mov %esp,%ebp  
4b0: 53 push %ebx  
4b1: 83 ec 04 sub $0x4,%esp  
4b4: e8 eb ff ff ff call 4a4 <_\_i686.get\_pc_thunk.bx>  
4b9: 81 c3 4b 11 00 00 add $0x114b,%ebx  
4bf: e8 fc fe ff ff call 3c0 <foo@plt> // PLT thunk  
4c4: 83 c4 04 add $0x4,%esp  
4c7: 5b pop %ebx  
4c8: 5d pop %ebp

其中相关的 PLT 内容如下：

000003c0 <foo@plt>:  
3c0: ff a3 10 00 00 00 jmp *0x10(%ebx) // 指向跳转表项的指针  
3c6: 68 08 00 00 00 push $0x8  
3cb: e9 d0 ff ff ff jmp 3a0 <_init+0x18>

-fPIC 的情况很好理解，跳转都是通过 PLT 进行的，直接去修改跳转表中的数据即可。那么没有 -fPIC 的情况，loader 是怎么能够修改调用让他指向另外 .so 里面的函数呢？

从汇编里面可以看出来，没有 -fPIC 的情况下面，对 foo 的调用指向的也不是 foo ，而是一个没有意义的地址，观察 .so 的 reloc 信息可知，有一条 reloc 指向 bar 对 foo 调用的地方，内容是 "foo"

DYNAMIC RELOCATION RECORDS  
OFFSET TYPE VALUE  
000015fc R\_386\_RELATIVE \*ABS\*  
00001600 R\_386\_RELATIVE \*ABS\*  
000004a1 R\_386\_PC32 foo

因此在 LD_PRELOAD 的情况下面， loader 会查找 foo 的地址并填入 bar 对 foo 调用的地方。

这种按照名字 reloc 的方式在 Win32 下面似乎没有见到过。Win32 下面 reloc 只是用于修正 DLL 加载以后导致数据指针位置不对的问题，操作方法是简单的把位置上面的值减掉原基址加上新基址，相当于 R\_386\_RELATIVE 这种类型的 reloc。但是仔细想想，这种 reloc 其实并没有任何必要，RELATIVE 方式已经足够解决问题了，怀疑这个 PC32 的方式，就是单独为了支持 LD_PRELOAD 这一历史遗留产物而设计的。(我比较懒得查阅资料，谁查到了麻烦讲一下)

在 64 位下面，这个把戏不能用了，因为 64bit 下面函数调用一般还只是通过 E8 指令来调用 32 位范围内的地址，而 foo 函数的位置可能离 bar 调用它的地方超过了 2G，因此 ld 遇到这样的 reloc 会报告失败，要求你必须用 -fPIC 来编译。

有人可能会问， -fPIC 了不是会影响性能？这个确实没办法了，在很久以前大家没有意识到需要 module local symbol ，觉得除了 static 的所有函数都全局可见就好了，结果这个习惯就一直继承下来了，虽然现在 ELF 格式中确实支持 module local 的 symbol。但是因为历史上就是这么下来的，为了维持兼容性，只好继续保持这一行为。很多函数并没有跨模块调用的需要，只是因为有跨文件调用就不得不变成非 static 的，在上面说的这种情况下就会受到影响。

一个很土鳖的解决方法就是把所有函数都堆进一个 .c ，然后除了真正需要导出的函数，都改成 static 的。也许 gcc 现在支持什么扩展，可以将符号声明成 module local 的，不过我也是懒得查资料了，有人知道的话，请不吝赐教。 //bow