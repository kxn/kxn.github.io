---
title: UE4 用 fastbuild 实现并行编译 Shader
author: kxn
type: post
date: 2019-11-06T08:45:20+00:00
url: /index.php/archives/409
categories:
  - Tech Notes

---
其实也是用网上代码改的，参见这里 https://github.com/fastbuild/fastbuild/issues/539

然后自己集成进去就可以，参考 XGE 的实现即可，几个需要改的地方  
1: UE4 ShaderCompiler.cpp 里面有个地方把 Shader mapping directory 从相对路径给转换成绝对路径了，这个需要去掉，同时保证UE4引擎和项目处于同一个盘上。  
2: 编译机器上需要把 VC 2017 redistributable 和 DX 的 runtime 都装上，前者有很多地方可以下载，后者搜索 dxwebinstaller，从微软站上下了以后把 directx 的库都装齐  
3: 依赖关系需要增加一些，需要修改 ShaderCompilerFASTBuild.cpp 里面，之前只有 .ush 和 .usf 的依赖，请照葫芦画瓢增加 .h .hlsl .glsl 的依赖，此外依赖文件除了 Engine\\Binaries\\ThirdParty\\Windows\\DirectX\\x64\\d3dcompiler_47.dll 之外，还需要增加下面这几个依赖  
Engine\\Binaries\\ThirdParty\\Windows\\DirectX\\x64\\WinPixEventRuntime.dll  
Engine\\Binaries\\Win64\\dxcompiler.dll  
Engine\\Binaries\\Win64\\dxcompiler_sc.dll  
Engine\\Binaries\\Win64\\dxil.dll

加上以后重新编译 UE4，配置好 fastbuild 即可，比花钱买 IncrediBuild 还是划算很多。