---
layout: post
title: PHP中MySQL、MySQLi和PDO的用法和区别
categories: PHP
description: 对于一个初学PHP的自己，对数据库的连接有着很大的疑惑，于是查阅了很多资料及自己实际操作，写下自己的一点总结笔记。
keywords: MySQL,MySQLi,PDO
---

PHP的MySQL扩展(优缺点)

设计开发允许PHP应用与MySQL数据库交互的早期扩展。mysql扩展提供了一个面向过程 的接口；

并且是针对MySQL4.1.3或更早版本设计的。因此，这个扩展虽然可以与MySQL4.1.3或更新的数 ；

据库服务端 进行交互，但并不支持后期MySQL服务端提供的一些特性。

PHP的mysqli扩展

mysqli扩展，我们有时称之为MySQL增强扩展，可以用于使用 MySQL4.1.3或更新版本中新的高级特性；

mysqli扩展在PHP 5及以后版本中包含；

mysqli扩展有一系列的优势，相对于mysql扩展的提升主要有：面向对象接口、 prepared语句支持、多语句执行支持、事务支持、增强的调试能力、嵌入式服务支持。

PHP数据对象(PDO)

PHP数据对象，是PHP应用中的一个数据库抽象层规范。PDO提供了一个统一的API接口可以，使得你的PHP应用不去关心具体要 连接的数据库服务器系统类型。也就是说，如果你使用PDO的API，可以在任何需要的时候无缝切换数据库服务器。

下面是我这个星期做过的几种数据库连接的方式：

Mysql连接：

```php
<?php

$conn = @ mysql_connect("localhost", "root", "") or die("数据库连接错误");

mysql_select_db("bbs", $conn);

mysql_query("set names 'utf8'");

echo "数据库连接成功";

?>

```
Mysqli连接：

```php
<?php

$conn = mysqli_connect('localhost', 'root', '', 'bbs');

if(!$conn){

die("数据库连接错误" . mysqli_connect_error());

}else{

echo"数据库连接成功";

}

?>

```
Pdo连接：

```php
<?php

try{

$pdo=new pdo("mysql:host=localhost;dbname=bbs","root","");

}catch(PDDException $e){

echo"数据库连接错误";

}

echo"数据库连接成功";

?>

```
以下是我在网站看到的Mysql、Mysqli、Pdo三种方式的区别对比

*********************************************************************************************************************************
PHP-MySQL 是 PHP 操作 MySQL 资料库最原始的 Extension ，PHP-MySQLi 的 i 代表 Improvement ，

提更了相对进阶的功能，就 Extension 而言，本身也增加了安全性。

而 PDO (PHP Data Object) 则是提供了一个 Abstraction Layer 来操作资料库

1.mysql与mysqli

mysqli是php5提供的新函数库，(i)表示改进，其执行速度更快.当然也更安全

mysql是非持继连接函数而mysqli是永远连接函数。也就是说

mysql每次链接都会打开一个连接的进程而mysqli多次运行mysqli将使用同一连接进程,从而减少了服务器的开销 有些朋友在编程的时候，使用new mysqli(‘localhost’, usenamer’, ‘password’, ‘databasename’);总是报

错，Fatal error: Class ‘mysqli’ not found in d:\…

mysqli类不是php自带的吗？

不是默认开启的，win下要改php.ini,去掉php_mysqli.dll前的;,linux下要把mysqli编译进去。

一:Mysqli.dll是一个允许以对象的方式或者过程操作数据库的，它的使用方式也很容易。这里就几个常

```php
<?php

mysql_connect($db_host, $db_user, $db_password);

mysql_select_db($dn_name);

$result = mysql_query("SELECT `name` FROM `users` WHERE `location` = '$location'");

while ($row = mysql_fetch_array($result, MYSQL_ASSOC))

{

echo $row['name'];

}

mysql_free_result($result);

?>

```
其实背后有些学问… 这种方式不能 Bind Column ，以前例的 SQL 叙述来说，$location 的地方容易被 SQL Injection。后来于是发展出了 mysql_escape_string() (备注：5.3.0之后弃用) 以及 mysql_real_escape_string()来解决这个问题，不过这麽一搞，整个叙述会变得複杂且丑陋，而且如果栏位多了，可以想见会是怎样的情形…

```php
<?php

$query = sprintf("SELECT * FROM users WHERE user='%s' AND password='%s'",

mysql_real_escape_string($user),

mysql_real_escape_string($password));

mysql_query($query);

?>

```
在 PHP-MySQLi 中有了不少进步，除了透过 Bind Column 来解决上述问题，而且也多援 Transaction, Multi Query ，

并且同时提供了 Object oriented style (下面这段 PHP-MySQLi 范例的写法) 和 Procedural style

```php
<?php

$mysqli = new mysqli($db_host, $db_user, $db_password, $db_name);

$sql = "INSERT INTO `users` (id, name, gender, location) VALUES (?, ?, ?, ?)";

$stmt = $mysqli->prepare($sql);

$stmt->bind_param('dsss', $source_id, $source_name, $source_gender, $source_location);

$stmt->execute();

$stmt->bind_result($id, $name, $gender, $location);

while ($stmt->fetch())

{

echo $id . $name . $gender . $location;

}

$stmt->close();

$mysqli->close();

?>

```
但看到这边又发现了一些缺点，例如得 Bind Result，这个就有点多馀，不过这其实无关紧要，因为最大的问题还是在于这不是一个抽象(Abstraction)的方法，所以当后端更换资料库的时候，就是痛苦的开始… 于是 PDO 就出现了

2.PDO与mysql

PDO是PHP5.1之后才支持的，他为访问数据库采用了一致性的接口。但是国内众多的开源程序都是

使用MySQL的extension所提供的function连接数据库，进行查询。PDO功能强大为何国内成熟的PHP系统都不使用呢？

问过几个朋友为啥用PDO,答案是“快”，PDO连接数据库会快么？为什么使用PDO?他们两种方式有什么区别？首先还是比较关心的性能问题.写了1个脚本测试向MySQL插入100万条数据。

```php
<?php

$link = mysql_connect("localhost", "root", "root") or die('mysql connect error');

$num = 100000;

$dsn = "mysql:host=127.0.0.1;dbname=performace_test";

$db = new PDO($dsn, 'root', 'root', array(PDO::ATTR_PERSISTENT => true));

mysql_query('TRUNCATE TABLE `performace_test`.`myquery`',$link);  //Truncate Table

$query = "INSERT INTO `performace_test`.`myquery`(`goods_id`,`cat_id`,`click_count`,`goods_number`) VALUES ( ’80′,’298′,’65′,’100′)";

$start_time = microtime(true);

for($i=0;$i<$num;$i++)

{

mysql_query($query,$link);

}

echo "USE MySQL extension: ". (microtime(true)-$start_time);

mysql_query(‘TRUNCATE TABLE `performace_test`.`myquery`’,$link);  //Truncate Table

$start_time = microtime(true);

for($i=0;$i<$num;$i++)

{

$db->exec($query);

}

echo "\r\nUSE PDO : ". (microtime(true)-$start_time);

?>

```
USE MySQL extension: 95.233189106s

USE PDO : 99.1193888187s

在链接MySQL上几乎没有区别。PDO的性能损失完全可以忽略不计。

但是却有非常多的操作却是MySQL扩展库所不具备的：

1:PDO真正的以底层实现的统一接口数库操作接口

2:PDO支持更高级的DB特性操作，如：存储过程的调度等,mysql原生库是不支持的.

3:PDO是PHP官方的PECL库，兼容性稳定性必然要高于MySQL Extension,可以直接使用 pecl upgrade pdo 命令升级.

PHP6默认也是使用PDO进行数据库链接，MySQL Extension会作为辅助。所以我们在日常项目中，如果环境允许，尽可能去使用PDO来进行MySQL数据库操作。