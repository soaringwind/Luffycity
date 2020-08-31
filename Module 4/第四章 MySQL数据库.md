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

## 库操作

### 增删改查

```
create database db1;

drop database db1;

alter database db1 charset='utf8';

show create database db1;

show databases;
```



## 表操作

### 存储引擎（表类型）

针对不同的文件，应该选择不同的文件格式进行保存，根据不同的格式选择不同的处理机制。例如，如果想要速度，可以把使用内存存储。存储引擎，就是如何存储数据，如何为存储的数据建立索引和如何更新、查询数据等技术的实现方法。

查看所有支持的储存引擎：show engines;

查看当前的默认存储引擎：show variables like "default_storage_engine"

![image-20200830133357987](C:\Users\soaringwind\AppData\Roaming\Typora\typora-user-images\image-20200830133357987.png)



MySQL主要的存储引擎：innodb、mysiam、memory、blackhole

#### Innodb

MySQL5.5版本之后的默认存储引擎，support transactions, row-level locking, and foreign keys，支持事务，支持表锁，支持行锁。在并发时，能保证数据的一致性

存储时两个文件，frm文件和ibd文件，frm存放表结构，ibd存放表数据。

#### Myisam

MySQL5.5版本之前的默认存储引擎，查询速度快，支持表锁，适合于只读的场景。

存储时三个文件，frm表结构，MYD表数据，MYI索引。因此查找速快。

#### Memory

内存引擎，数据全部存放在内存中，一旦断电数据全部丢失。

存储时一个文件，frm表结构。

#### Blackhole

无论存任何内容，都会立刻消失。

存储时一个文件，frm表结构。

![img](https://img2018.cnblogs.com/blog/827651/201809/827651-20180928181850055-806373159.png)

MySQL的架构设计：

```
MySQL架构总共四层，在上图中以虚线作为划分。
　　首先，最上层的服务并不是MySQL独有的，大多数给予网络的客户端/服务器的工具或者服务都有类似的架构。比如：连接处理、授权认证、安全等。
　　第二层的架构包括大多数的MySQL的核心服务。包括：查询解析、分析、优化、缓存以及所有的内置函数（例如：日期、时间、数学和加密函数）。同时，所有的跨存储引擎的功能都在这一层实现：存储过程、触发器、视图等。

　　第三层包含了存储引擎。存储引擎负责MySQL中数据的存储和提取。服务器通过API和存储引擎进行通信。这些接口屏蔽了不同存储引擎之间的差异，使得这些差异对上层的查询过程透明化。存储引擎API包含十几个底层函数，用于执行“开始一个事务”等操作。但存储引擎一般不会去解析SQL（InnoDB会解析外键定义，因为其本身没有实现该功能），不同存储引擎之间也不会相互通信，而只是简单的响应上层的服务器请求。

　　第四层包含了文件系统，所有的表结构和数据以及用户操作的日志最终还是以文件的形式存储在硬盘上。
```



### 创建表的完整语法

```mysql
create table 表名(
	字段名1 类型(宽度) (约束条件), 
	字段名2 类型(宽度) (约束条件)
)
create table t1(id int name char(16)) engine=myisam;
alter table t1 engine=innodb;
```

注意：

1. 同一张表中的字段名不能重复。
2. 宽度和约束条件是可以不写的，而字段名和类型则是必须的。
3. 约束条件可写多个。
4. 最后一行不能有逗号。

表的增删改查：

```mysql
修改表名：alter table table_test rename table_test1;

增加字段：alter table table_test add 字段名 数据类型 (约束条件);
插入到首行：alter table table_test add 字段名 数据类型(约束条件) first;
插入到某行之后：alter table tale_test add 字段名 数据类型(约束条件) after 字段名;

删除字段：alter table table_test drop 字段名;

修改字段数据类型：alter table 表名 modify 字段名 新数据类型 (约束条件);
修改字段名：alter table 表名 change 旧字段名 新字段名 旧数据类型 (约束条件);
修改字段约束条件：alter table 表名 change 旧字段名 新字段名 新数据类型 (约束条件);
```

### 基本的数据类型

#### 数值类型

类型很多，记住，整数用int，小数float，够平时使用。

![image-20200830145712618](C:\Users\soaringwind\AppData\Roaming\Typora\typora-user-images\image-20200830145712618.png)

默认的数值类型都是带有符号的，存储数据的量会少一半，因此想要无符号的，需要添加约束条件。

数值类型括号内的数字代表限制的字节数量，但基本不需要指定。

#### 日期类型

使用now()可以直接添加当前时间。

![image-20200830150759578](C:\Users\soaringwind\AppData\Roaming\Typora\typora-user-images\image-20200830150759578.png)

![image-20200830151333464](C:\Users\soaringwind\AppData\Roaming\Typora\typora-user-images\image-20200830151333464.png)



#### 字符串类型

![image-20200830151606859](C:\Users\soaringwind\AppData\Roaming\Typora\typora-user-images\image-20200830151606859.png)

char：浪费空间，但是存取简单，按固定字符存取。

varchar：节省空间，存取麻烦，不知道取几位，存的时候需要制作报头

以前基本用char，现在用varchar也挺多。



#### enum和set类型

![image-20200830151855371](C:\Users\soaringwind\AppData\Roaming\Typora\typora-user-images\image-20200830151855371.png)

enum：只能选一个出来。

set：可以选多个，但不能超出可选范围，并且会自动去重。

### 约束条件

unsigned：无符号

```mysql
use db1;
select database();

create table t1 (
id int unsigned,  # 插入有符号的，会自动变成0
name char(8)
);
```



not null：不为空

```mysql
create table t1(
id int unsigned, 
name char(8) not null);
```



default：默认值

```mysql
create table t1(
id int unsigned, 
name char(8) not null, 
sex enum('male', 'female') default 'male');
```



unique：唯一

```mysql
create table t1(
id int unsigned unique, 
name char(8) not null, 
sex enum('male', 'female') default 'male');

# 联合唯一
create table t1(
ip char(15), 
server char(10), 
port int, 
unique(ip, port));  # 两个都相同时，报错。
```



auto_increment：整数自增，不为空且唯一，且必须同时被key约束

```mysql
create table t1(
id int primary key auto_increment, 
name char(8) not null, 
sex enum('male', 'female') default 'male');
```



primary key：主键，唯一且不为空

```mysql
create table t1(
id int primary key, 
name char(8) not null, 
sex enum('male', 'female') default 'male');

# 联合主键和联合唯一类似
```



foreign key：外键，创建时需要先创建外键的表，且外键的关联字段必须唯一。

```mysql
create table t1(
id int primary key auto_increment, 
name char(8) not null, 
sex enum('male', 'female') default 'male', 
hobby set('basketball', 'football', 'tennis'), 
position char(16), 
foreign key(position) references department (name));  # 这里department的name必须约束唯一

# 级联删除和级联更新

foreign key (position) references department(name) on update cascade on delete cascade;
```



### 表与表关系

分析两张表中的数据之间的关系。

一对多：将多的那个表设置成外键。

一对一：后出现的表设置成外键。

多对多：产生第三张表，把两个有着关联关系的字段作为第三张表的外键。



## 记录的增删改查

insert：增，insert into 表名 (字段1, 字段2....) values (值1, 值2.....)

```mysql
insert into t1 values (1, 'alex');
insert into t1(id, name) values(2, 'egon'), (3, 'peiqi');

# 如果设置自增，则可以少写
insert into t1(name) values('yuan')
```



delete：删，delete from 表名 where 条件;

```mysql
delete from t1 where id=1;
```



update：改，update 表名 set 字段=新的值 where 条件;

```mysql
update t1 set name='sdhfisdf' where id=2;
```



select：查，select * from 表，select 字段1, 字段2... from 表，select distinct 字段1, 字段2... from 表（去重），select name, salary*12 as annual_salary from 表（运算）

```mysql
select * from t1;
select id, name from t1;
select distinct id, name, position from t1; 
select name, salary*12 as annual_salary from t1;
```



作业

```mysql
create database db1;
use db1;

# 创建班级表
create table class_table (
cid int unique primary key auto_increment,
caption char(8) unique
);

# 添加内容
insert into class_table(caption) values('三年二班'), ('一年三班'), ('三年一班');

# 创建学生表
create table student_table(
sid int unique primary key auto_increment, 
sname char(8), 
gender enum('女', '男') default '男', 
class_id int,
foreign key (class_id) references class_table(cid)
);

# 添加内容
insert into student_table(sname, gender, class_id) values(
'钢蛋', '女', 1), ('铁锤', '女', 1), ('山炮', '男', 2);
```



































