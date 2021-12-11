---
title: Delphi 程序在非管理员权限下启动时候的 bug
author: kxn
type: post
date: 2006-05-05T06:10:30+00:00
url: /post/119.html
views:
  - 3894
categories:
  - Tech Notes
---

diode@newsmth 指出 FTerm 在普通用户帐号下面第一次启动会报错，怀疑是注册 COM 组件的问题，跟踪了一下还真是

在 ComServ.pas 里面有这么一段  
<coolcode>  
procedure TComServer.Initialize;  
begin  
try  
UpdateRegistry(FStartMode <> smUnregServer);  
except  
on E: EOleRegistrationError do  
// User may not have write access to the registry.  
// Squelch the exception unless we were explicitly told to register.  
if FStartMode = smRegServer then raise;  
end;  
if FStartMode in [smRegServer, smUnregServer] then Halt;  
ComClassManager.ForEachFactory(Self, FactoryRegisterClassObject);  
end;  
</coolcode>

UpdateRegistry 的时候就抛异常了，但是看注释里面 Delphi 应该可以避免这个问题啊？仔细一看，原来抛出的异常是 EOleSysError 不是 EOleRegistrationError，实在是晕菜。

Google 一下可以找到两篇关于这个的，

<http://qc.borland.com/wc/qcmain.aspx?d=1768>  
<http://qc.borland.com/wc/qcmain.aspx?d=3015>

原来 Delphi 5.0 时候就有这毛病，到 7.0 也没有改，不知道该说什么好。

解决方法只能修改 Delphi 自己的运行库了，因为这个是在 Application.Initialize 里面由框架去调的，我在外面捕捉不管事。

下面的这个修改方法是照上面两篇东西里面一篇抄的。修改 ComServ.pas 以后，build 一下，就好了。。

<coolcode>  
procedure TComServer.Initialize;  
begin  
try  
UpdateRegistry(FStartMode <> smUnregServer);  
except  
on E: EOleRegistrationError do  
// User may not have write access to the registry.  
// Squelch the exception unless we were explicitly told to register.  
if FStartMode = smRegServer then raise;  
// FIX from NET end  
on E: EOleSysError do  
if (E.ErrorCode <> TYPE\_E\_REGISTRYACCESS) or (FStartMode = smRegServer) then  
raise;  
// FIX from NET end  
end;  
if FStartMode in [smRegServer, smUnregServer] then Halt;  
ComClassManager.ForEachFactory(Self, FactoryRegisterClassObject);  
end;  
</coolcode>
