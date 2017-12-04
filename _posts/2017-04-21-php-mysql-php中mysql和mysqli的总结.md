---
layout:     post
title:      php中mysql和mysqli的总结
subtitle:   推荐学习和使用mysqli
date:       2017-04-21
author:     Liangyuqi
header-img: img/post-bg-2.jpg
catalog: true
tags:
    - mysql
    - php
---

>首先php—mysql 是 php 操作 mysql 资料库最原始的的拓展

>而php—mysqli，字母i代表的 Improvement ，提更了相对进阶的功能。

### mysql和mysqli区别
mysql是非持继连接函数而mysqli是永远连接函数

也就是说mysql每次链接都会打开一个连接的进程

而mysqli多次运行mysqli将使用同一连接进程,从而减少了服务器的开销 

 

默认情况下，**从php5开始，php不在自动开启对mysql的支持**，而是放到扩展函数库中。所以用户需要在拓展函数库中开启mysql函数库

首先打开php.ini 找到“；entensions=php_mysql.dll”，去掉前面的分号注释

保存重启apache或者iis

然后可以通过phpinfo（）函数检查是否配对成功，如果显示出的php的环境配置信息中有mysql项目，表示已经成功开启对mysql的支持。


mysqli.dll是一个允许以对象的方式或者过程操作数据库的，它的使用方式也很容易。这里就几个常见的操作和 mysql.dll做一个对比。
### 代码演示
#### mysql.dll(可以理解为函数式的方式)： 

```
	$conn = mysql_connect('localhost', 'user', 'password');　//连接mysql数据库 
	mysql_select_db('data_base'); //选择数据库 　 
	$result = mysql_query('select * from data_base');//这里有第二个可选参数，指定打开的连接 
	$row = mysql_fetch_row( $result ) ) //为了简单，这里只取一行数据 
	echo $row[0]; //输出第一个字段的值 
```
mysqli也有过程式的方式，只不过开始贯以mysqli的前缀，其他都差不多。

如果mysqli以过程式的方式操作的话， 有些函数必须指定资源

比如说 mysqli_query(资源标识,SQL语句),并且资源标识的参数是放在前面的

而 mysql_query(SQL语句,'可选')的资源标识是放在后面的，并且可以不指定，它默认是上一个打开的连接或资源。

而且mysql_select_db和mysqli_select_db的参数刚好相反。 

这里需要重点强调的是  **php5.5无法使用mysql_query,需要用mysqli**

所以我才接触到了mysqli 手动滑稽
![](http://images2015.cnblogs.com/blog/1017580/201704/1017580-20170420205832993-730184404.png)


#### mysqli.dll(对象方式)：

```
   $conn = new mysqli('localhost', 'user', 'password','data_base'); 
　　//这里的连接是new出来的，最后一个参数是直接指定数据库，不用mysql_select_db()了 
　　//也可以构造时候不指定，然后 $conn -> select_db('data_base') 
　　$result = $conn -> query( 'select * from data_base' ); 
　　$row = $result -> fetch_row(); //取一行数据 
　　echo row[0]; //输出第一个字段的值 
```

