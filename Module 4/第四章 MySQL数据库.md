# MySQL数据库

## 数据库概述

定义：长期存放在计算机内的有组织、可共享的数据。

本质：基于网络通信的应用程序。

两种类型数据库：

1. 关系型数据库：MySQL、Oracle、db2、access、sql server，数据之间彼此有关系或者约束，表现形式通常为表格形式，且每个字段可以限制存储的类型。
2. 非关系型数据库：redis、mongodb、memcache，以k，v键值对的形式存储。

几个重要概念：

	1. 数据库：文件夹。
 	2. 表：文件，新建的时候需要输入表头及格式。
 	3. 记录：一行行的数据。
 	4. 表头：表格的第一行字段。
 	5. 字段：变量名。

## MySQL安装与基本管理

任何基于网络通信的应用程序底层用的都是socket（C/S架构）。

MySQL不单单支持自己的客户端app，还支持其他编程语言，但需要采用统一的语言来控制（即sql语句）。

### MySQL安装

安装5.6版本，自己学习，服务端和客户端都要写，在公司，服务端有专门的写。

MySQL服务端：mysqld.exe

MySQL客户端：mysql.exe

注意：cmd尽量使用管理员身份运行，win+r进入的是普通模式，有一些命令无法执行。

启动：使用cmd来启动，先切换目录到MySQL目录下，输入mysqld，保留窗口，作为服务端，之后，输入mysql，启动客户端。

```mysql
mysql -h 127.0.0.1 -p 3306 -uroot -p
```

常见软件的默认端口号

```
MySQL 3306
redis 6379
mongodb 27017
django 8000
flask 5000
tomcat 8080
```

MySQL第一次以管理员运行，不需要输入密码。

### 基础的sql语句

```
1. MySQL的sql语句是以分号作为结束标志

2. 基本命令
	show databases：查看所有的库名。
	
3. 连接服务端命令可以简写
	mysql -uroot -p
	
4. 输入命令不对，用\c取消（cancel）

5. 退出：quit，exit

6. 只输入mysql也能连接，但只是游客模式，很多功能访问不到。
```

### 环境变量的配置及系统服务制作

补充知识点：

```
1. 查看当前进程：tasklist              tasklist | findstr mysqld

2. 杀死进程：taskkill /F /PID PID号（管理员）。
```

环境变量配置：将mysqld所在的文件路径，添加到系统环境变量中。

将mysqld服务端制作成系统服务（开机自启动），个人觉得用处不大。

```
mysqld --install
mysqld --remove
```

设置密码

```
mysqladmin -uroot -p 原密码 password 新密码
```

忘记密码

个人觉得意义不大，如果真的忘记了，再自己想办法。

修改配置文件

```
mysql默认的配置文件：
	my-default.ini   # ini结尾一般都是配置文件
```

```mysql
# 新建一个my.ini的配置文件，来统一编码
[mysqld]
character-set-server=utf8
collation-server=utf8_general_ci
[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
```

## 基础sql语句

sql(Structured Query Language)结构化查询语句：用于存取数据、查询数据、更新数据和管理关系数据库系统，由IBM开发，sql语言分为三种：

```
1. DDL语句（Data Definition Language, 数据库定义语言）：创建、修改、删除，数据库、表、视图、索引、存储过程，例如create，drop，alter

2. DML语句（Data Manipulation Language, 数据库操纵语言）：插入数据instert，删除数据delete，更新数据update，查询数据select

3. DCL语句（Data Control Language, 数据库控制语言）：设置或更改用户权限的语句，grant，revoke，deny等
```

针对库的增删改查（文件夹）

```
增：create database db1 charset='utf-8';

查：show databases;  # 查看所有
	show create database db1;  # 查看单个
改：alter database db1 charset='gbk'; 

删：drop database db1;  # 删库跑路，哈哈
```

针对表的增删改查（文件）

```
# 操作表（文件）的时候，需要指定文件所在的库（文件夹）

查看当前所在库：select database();

切换库：use db1;

增：create table t1(id int, name char(4));  # 增加需要放上字段
	create table 'db1.t2'(id int, name char(4))  # 使用绝对路径
	
查：show tables;  # 查看当前库所有表
	show create table t1; 
	describe t1;
	desc t1;  # 支持简写

改：alter table t1 modify name char(16);
	alter table t1 change name name1 char(2);

删：drop table t1;
```

针对数据的增删改查（一行行数据）

```
# 一定要先有库，后有表，最后才有记录

增：insert (into) t1 values(1, 'json'), (2, 'json1');

查：select * from t1;  # 当数据量较大时，不推荐使用
	select id, name from t1; 

改：update t1 set name='DSB' where name='json';

删：delete from t1 where id > 1;
	delete from t1 where name='json';

清空当前表的所有数据：delet from t1;
```

## 表操作

### 存储引擎

































