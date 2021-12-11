---
title: 用 swig 给脚本语言做 wrapper
author: kxn
type: post
date: 2005-12-12T14:45:06+00:00
url: /index.php/archives/35
views:
  - 3231
categories:
  - Coding
  - Tech Notes

---
　　工程里面有时候需要将某些C或者C++编写的模块封装成脚本语言的扩展，以便在脚本语言里面调用，这个工作通常很枯燥，会写出来很多重复代码，人一向都是会偷懒的动物，因此就诞生了一些工具来帮助人们做这件事情，其中比较有名的就是 swig, 此外还有 boost::python 等，不过我觉得还是 swig 最好，因为他可以同时支持很多种脚本语言。  
　　<a title="Simplified Wrapper and Interface Generator" target="_blank" href="http://www.swig.org/">SWIG</a> 是一种接口编译器，专门用来连接 C/C++ 程序和脚本语言的，它可以直接处理 C/C++ 头文件里面的声明，生成包装代码。和原有代码编译链接以后，生成多种脚本语言的扩展，可以加载进脚本语言解释器中使用。经过不少年的发展，他已经对C/C++语言支持得非常好了，甚至包括对模版的支持。swig 生成的代码基本都是可以直接用的，不需要再手动填空。他支持的脚本语言更是非常之多，还包括一些编译类型的伪码语言比如 Java。我的Fedora Core 4 系统带的 swig 1.3.24 支持这些脚本语言：ALLEGROL，CHICKEN，C#，GUILE，Java，Modula3，Mzscheme，Ocaml，Perl，PHP，Pike，Python，Ruby，Lisp，Tcl，XML(???这个怎么都有)。很多都没有听说过，不过常用的Perl/PHP/Python都是支持的。  
　　swig 接受的输入是类似 C 的头文件一样的一种声明文件，支持很多以 % 开头的宏。同时他可以识别C/C++语言的声明，从而对应生成包装代码，常见的数据结构都可以建立对应关系，例如 int <-> int, char * <=> string 等等。下面是一个例子：

　　fileio.i  
%module fileio  
　　%{  
　　/\* 这里放的是后面声明所需要的其他 header ，以及一些私有声明之类，会被 swig 原封抄到生成的代码里面去\*/  
　　#include <stdio.h>  
　　%}  
　　/\* 这后面写需要生成包装代码的方法和对象声明，这个例子是C的，因此只有简单的函数声明，虽然 stdio.h 里面声明了很多函数和数据类型，但是我们想要包装的只有 fopen 和 fclose 两个函数 \*/

　　FILE \*fopen(const char \*filename, char *mode);  
　　int fclose(FILE *fp);  
这就是一个简单的 swig 输入文件, %module 是给这个扩展起名字，将作为这个扩展在脚本语言中的名字，如果在python里面，使用时候就要import fileio。

　　用 swig 处理 fileio.i 会得到一个 .c 或者 .cxx 文件，取决于 swig 是否加了 -c++ 参数。如果用 c++ 方式处理，那么 swig 生成代码会有所不同，比如分配内存会用　new 来作，但总的来说，区别不大。

　　[root@kangkang.org ~] swig -python -o fileio_wrap.c fileio.i

　　然后  
　　[root@kangkang.org ~] gcc -shared -o \_fileio.so -I/usr/include/python2.4 fileio\_wrap.c

　　本例里面因为我们没有任何现有代码，封装的是libc里面已经有的 fopen 和 fclose ，因此只要单独编译包装代码 fileio_wrap.c 就可以了，如果是封装已有工程的代码，就需要将已有工程代码一起编译链接成动态库。

　　在执行 swig 的时候，swig 除了生成 fileio\_wrap.c 包装代码之外，还会负责生成脚本语言端需要的声明代码，本例中它会生成 fileio.py，负责声明和加载 \_fileio.so ，在 python 运行环境里面，只要 import fileio ，就可以自动载入 _fileio.so 。然后就可以使用 fileio.fopen 和 fileio.fclose 了。

　　这只是一个简单的例子，实际 swig 的功能远比这个例子来得强大，需要进一步了解的，可以读他的<a title="SWIG-1.3 Development Documentation" target="_blank" href="http://www.swig.org/Doc1.3/Contents.html#Contents">说明文档</a>