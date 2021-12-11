---
title: 'MSVCR80.DLL 里面的黑手& fick Microsoft'
author: kxn
type: post
date: 2005-12-06T05:40:43+00:00
url: /index.php/archives/18
views:
  - 7548
categories:
  - Coding
  - Tech Notes

---
继续昨天关于 VC8 manifest 的话题

今天试验了一下果然不行。2000 之类不支持 winsxs 的系统都可以跑，xp 和 2k3 下面，都说没有正常设置，如果手动删掉 exe 里面的 manifest 的话，则是出个 assert, 说用不正常的方式 load dll

google 了一下，找到了某德国牛人的文章

http://blog.kalmbachnet.de/

Using the VC 2005 shared CRT/MFC (DLL) without a manifest is not supported!  
If you build your app with VC2005 and you accidently have disabled the embedding of the manifest file (or deleted the separate appname.exe.manifest file), you will get an error on XP and later!

This has to do with the checking for a valid manifest of the EXE inside the CRT/MFC DLLs “DllEntry” (via a call to \_check\_manifest). If the OS supports manifests (or better Side-By-Side assemblies/DLLs) the CRTs´ DLL forces the check of the EXEs´ manifest. If there is no (valid) manifest, then the DLL refuses to load (returns 0) and therefor the EXE cannot be started. It might display the following error message (or similar):  
R6034  
An application has made an attempt to load the C runtime library incorrectly.  
Please contact the application’s support team for more information.

But in the following cases the manifest checking inside the CRT-DLL is not done:

Pre-fusion OS (FindActCtxSectionStringW not found in kernel32.dll)  
Loaded by instrumented-mscoree.dll (mscoree.dll and pgort80.dll is already loaded)  
The path returned with GetModuleFileNameW of the DLL is longer than 8000 characters  
The path to the DLL, returned by GetLongPathName is longer than 8000 characters 

Microsoft 太变态了。faint, 于是按照该牛人的说法，注释掉 crt src 里面的 \_check\_manifest 调用， build 出来一个 msvcr80.dll , 和删掉 manifest 的 exe 配合使用就可以在任何系统上面使用了。 

再次 fick microsoft.