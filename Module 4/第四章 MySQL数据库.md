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
mysql -h 127.0.0.1 -P 3306 -uroot -p
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

快的原因：

```
1. innodb要缓存数据块，而myisam只缓存索引块。

2. innodb寻址要映射到块，再到行，而myisam记录的直接是文件的offset，定位很快。

3. innodb要维护MVCC（multi-version concurrency control）多版本并发控制，也就是innodb支持事务，需要处理锁。
```



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
插入到首列：alter table table_test add 字段名 数据类型(约束条件) first;
插入到某列之后：alter table tale_test add 字段名 数据类型(约束条件) after 字段名;

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

注意：外键和主键的数据类型必须相同。

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

# 创建老师表
create table teacher_table(
tid int unique primary key auto_increment, 
tname char(8));

# 添加内容
insert into teacher_table(tname) values('波多'), ('苍空'), ('饭岛');

# 创建课程表
create table course_table(
cid int unique primary key auto_increment, 
cname varchar(8), 
teacher_id int,
foreign key (teacher_id) references teacher_table(tid));

# 添加内容
insert into course_table(cname, teacher_id) values('生物', 1), ('体育', 1), ('物理', 2);

# 创建成绩表
create table score_table (
sid int unique auto_increment, 
student_id int, 
course_id int, 
score int, 
foreign key (student_id) references student_table(sid), 
foreign key (course_id) references course_table(cid));

# 添加内容
insert into score_table(student_id, course_id, score) values(1, 1, 60), (1, 2, 59), (2, 2, 100);
```

### 单表查询

单表查询语法

```
select 字段1, 字段2... from 表名
                      where  条件
                      group by  分组
                      having  筛选
                      order by  排序
                      limit  限制条数
```

关键字的执行顺序

```
1. 找到表：from

2. 拿着where指定的约束条件，去文件/表中取出一条条记录

3. 将取出的一条条记录进行分组group by，如果没有group by，则整体作为一组

4. 将分组的结果进行having筛选

5. 执行select（去重）

6. 将结果按条件排序order by

7. 限制结果的显示条数
```

#### 简单查询

练习

```mysql
select concat('<名字:', emp_name, '>     ', '<薪资:'salary, '>') from employee;

select distinct post from employee;

select emp_name, salary*12 as annual_year from employee;
```

#### where约束

```
1. 比较运算符：<, >, =, >=, <=, <>, !=

2. between A and B：在A和B之间的值。
	经过实验，A、B为字符串也可以，但是字符串貌似顾头不顾尾。

3. in (A, B, C)：值是A或B或C

4. like 'n%'：模糊查询
    %（通配符）：表示任意多字符
    _（通配符）：表示任意一个字符

5. 逻辑运算符：与或非，and, or, not

6. 判断null时，必须使用成员运算，is或not is
```

练习

```mysql
select emp_name, age from employee where post='teacher';

select emp_name, age from employee where post='teacher' and age>30;

select emp_name, age, salary from employee where post='teacher' and salary between 9000 and 10000;

select * from employee where post_comment is not null;

select emp_name, age, salary from employee where post='teacher' and salary in (10000, 9000, 30000);

select emp_name, age, salary from employee where post='teacher' and salary not in (10000, 9000, 30000);

select emp_name, salary*12 as annual_salary from employee where post='teacher' and emp_name like 'jin%';
```

#### group by分组

单独使用group by关键字分组，会将group by后面的字段，将这个字段中的每一个不同的项保留下来，并且把值是这一项的归为一组，但是显示的时候，只显示第一个出现的信息。

```mysql
select post from employee group by post;
```

#### 聚合函数

把很多行的同一个字段进行统计，最终得到结果。聚合函数聚合的是组的内容，若是没有分组，则默认为一组。

```
count(字段)：统计这个字段有多少项。一般统计主键的个数，如果为空，则不统计。

sum(字段)：统计这个字段对应数值的和。

avg(字段)：统计这个字段对应数值的平均值。

min(字段)：统计这个字段中的最小值。

max(字段)：统计这个字段中的最大值。
```

练习

```mysql
select group_concat(emp_name), post from employee group by post;

select post, count(id) from employee group by post; 

select sex, count(id) from employee group by sex;

select post, avg(salary) from employee group by post;

select post, max(salary) from employee group by post;

select post, min(salary) from employee group by post; 

select sex, avg(salary) from employee group by sex;
```

#### having过滤

用法基本和where一样，但是having和where不一样的地方在于！！！

```
执行优先级从高到低：where > group by> having
1. where发生在分组group之前，因而where中可以有任意字段，但是绝对不能使用聚合函数。

2. having发生在分组group之后，因此having可以使用分组的字段，可以使用聚合函数。
```

练习

```mysql
select post, group_concat(emp_name), count(id) from employee group by post having count(id) < 2;

select post, avg(salary) from employee group by post having avg(salary) > 10000;

select post, avg(salary) from employee group by post having avg(salary) between 10000 and 20000;
```

#### order by查询排序

单列排序

```mysql
select * from employee order by salary;
select * from employee order by salary asc;
select * from employee order by salary desc;
```

多次排序：先按照规则1排序，如果有相同的，则按照规则2排序。

```mysql
select * from employee order by age, salary desc;
```

练习

```mysql
select * from employee order by age, hire_date desc;

select post, avg(salary) from employee group by post having avg(salary) > 10000 order by avg(salary);

select post, avg(salary) from employee group by post having avg(salary) > 10000 order by avg(salary) desc;
```

#### limit限制查询的记录数

分页功能，取前几个。

越到后面越慢的原因：limit虽然限制个数，但是每次都是将所有值取出，再去找索引，因此越到后面数据量越多，找的越慢。

```
limit n == limit 0, n
limit m, n ==从m+1往后取n个
limit n offset == limit m, n
最后一次取，取尽，不用考虑报错。
```

练习

```mysql
select * from employee limit 0,5;

select * from employee limit 5,5; 

select * from employee limit 10,5;
```

#### 使用正则表达式查询

regexp关键字表示正则表达式。

练习

```mysql
select * from employee where emp_name regexp '^jin.*[np]$'
```



### 多表查询

通常用于两张有关系的表（如一对多）中，当查询的内容涉及到两张表的时候，需要使用多表查询。

#### 连表查询（将两张表连在一起查）

##### 交叉连接

不适用任何匹配条件，生成笛卡儿积。

```mysql
# 笛卡儿积，单纯的把两张表一起查出来，总共数量n*m
select * from emp, department
```



##### 内连接

只连接匹配的行。

```mysql
select * from employee inner join department;   # 效果和笛卡儿积一样

# 没匹配上的不显示
select * from employee inner join department on employee.dep_id = department.id;
# 上面sql等于
select * from employee inner join department where employee.dep_id = department.id;
```



##### 外连接

```mysql
# 左连接：left join，优先显示左表的全部记录，跟右表对不上的，使用null来填充。
select * from employee left join department as dep on employee.dep_id = dep.id;

# 右连接：right join，优先显示右表的全部记录，跟左表对不上的，使用null来填充。
select * from employee right join department as dep on employee.dep_id = dep.id;

# 全外连接，在内连接的基础上增加上左、右两边没有的结果，MySQL不提供全连接，两个行数必须相等。
select * from employee left join department as dep on employee.dep_id = dep.id union select * from employee right join department as dep on employee.dep_id = dep.id;
```

练习

```mysql
# 以内连接的方式查询employee和department表，并且employee表中的age字段值必须大于25,即找出年龄大于25岁的员工以及员工所在的部门
select department.name from employee inner join department on employee.dep_id = department.id where employee.age>25;

select department.name from employee left join department on employee.dep_id = department.id where age > 25;

# 以内连接的方式查询employee和department表，并且以age字段的升序方式显示
select * from employee inner join department on employee.dep_id = department.id order by age;

select * from employee left join department on employee.dep_id = department.id order by age;
```



#### 子查询

```
1. 子查询是将一个查询语句嵌套在另一个查询语句中。
2. 内层查询语句的结果，可以作为外层查询语句提供查询条件。
3. 子查询中可以包含：in，not in，any，all，exists和not exists等关键字。
4. 还可以包含比较运算符：=，！=，>，<等
```

##### 带in关键字的子查询

```mysql
# 查询平均年龄在25岁以上的部门名
select name from department where id in (select dep_id from employee group by dep_id having avg(age)>25);

# 查看技术部员工姓名
select name from employee where dep_id = (select id from department where name='技术');

# 查看不足1人的部门名(子查询得到的是有人的部门id)
select name from department where id not in (select dep_id from employee group by dep_id);
```

##### 带比较运算符的子查询

```mysql
# 比较运算符：=，!=，>，<，>=，<=，<>
# 查询大于所有人平均年龄的员工名与年龄
select name, age from employee where age>(select avg(age) from employee);

# 查询大于部门内平均年龄的员工名、年龄
select t1.name, t1.age from employee as t1 inner join (select dep_id, avg(age) as avg_age from employee group by dep_id) as t2 on t1.dep_id=t2.dep_id where t1.age>t2.avg_age;
```

##### 带EXISTS关键字的子查询

```
EXISTS关键字表示存在，在使用EXISTS关键字时，内层查询语句不返回查询的记录，而是返回一个真假值，True或False，当返回True时，外层查询语句执行，当返回值False时，外层查询语句不进行查询。
```

```mysql
select * from employee where exists (select id from department where id=200);
select * from employee where exists (select id from department where id=204);
```

练习

```mysql
# 1. 分类找到最新日期
select post, max(hire_date) from employee group by post;

# 2. 内连接表，通过最新日期查找到名字
select t1.name, t1.post, t1.hire_date from employee as t1 inner join (select post, max(hire_date) as max_hire_date from employee group by post) as t2 on t1.post=t2.post where t1.hire_date=t2.max_hire_date;  # 不会错行去找
```



## PyMySQL模块

```python
import pymysql

conn = pymysql.connect(('127.0.0.1', 'root', "", 'db1'))

cur = conn.cursor()  # 创建一个游标对象，类似于文件

sql = 'show databases;'

cur.execute(sql)  # 执行sql

conn.commit()  # 提交到数据库执行

# 如何拿返回结果
res = cur.fetchone()  # 拿一条返回结果
res = cur.fetchall()  # 拿所有的返回结果
res = cur.fetchmany(5)  # 拿五条返回结果

conn.close()
```



## 索引原理

### 索引

#### 为什么要有索引

一般的操作系统，读写比例在10：1左右，插入数据和更新数据一般很少出错，但在实际情况中，我们遇到最多的则是查询操作，因此优化查询语句，提高查询速度，显然是重中之重。想要加速查询，就不得不提到索引。

#### 什么是索引

索引在MySQL中也叫做一种“键”，是存储引擎用于快速找到记录的一种数据结构。索引对于良好的性能来说非常关键，当数据量越来越大时，索引对性能的影响也就愈发重要。

```
索引类似于字典的偏旁部首表，如果不使用偏旁部首表，则需要从头到尾来查，效率很低，如果使用偏旁部首表，则可以缩小范围，然后再一个个查询，提高效率。
```

#### 索引的误解

索引太少会导致查询效率低下，而索引太多，则程序的性能又会收到很大的影响，尤其是写入的时候。因此，需要找到一个平衡点。

### 索引原理

索引的本质：通过不断地缩小想要获取数据的范围来筛选出最终想要的结果，同时把随机的事件变成顺序的事件。也就是说，有了这种索引机制，我们总是可以通过同一种查找方式来锁定数据。

```
数据库也是一样，但显然要复杂的多，因为不仅面临着等值查询，还有范围查询(>、<、between、in)、模糊查询(like)、并集查询(or)等等。数据库应该选择怎么样的方式来应对所有的问题呢？我们回想字典的例子，能不能把数据分成段，然后分段查询呢？最简单的如果1000条数据，1到100分成第一段，101到200分成第二段，201到300分成第三段......这样查第250条数据，只要找第三段就可以了，一下子去除了90%的无效数据。但如果是1千万的记录呢，分成几段比较好？稍有算法基础的同学会想到搜索树，其平均复杂度是lgN，具有不错的查询性能。但这里我们忽略了一个关键的问题，复杂度模型是基于每次相同的操作成本来考虑的。而数据库实现比较复杂，一方面数据是保存在磁盘上的，另外一方面为了提高性能，每次又可以把部分数据读入内存来计算，因为我们知道访问磁盘的成本大概是访问内存的十万倍左右，所以简单的搜索树难以满足复杂的应用场景。
# 搜索树
# 复杂度模型
```

磁盘IO与预读

```
前面提到了访问磁盘，那么这里先简单介绍一下磁盘IO和预读，磁盘读取数据靠的是机械运动，每次读取数据花费的时间可以分为寻道时间、旋转延迟、传输时间三个部分，寻道时间指的是磁臂移动到指定磁道所需要的时间，主流磁盘一般在5ms以下；旋转延迟就是我们经常听说的磁盘转速，比如一个磁盘7200转，表示每分钟能转7200次，也就是说1秒钟能转120次，旋转延迟就是1/120/2 = 4.17ms；传输时间指的是从磁盘读出或将数据写入磁盘的时间，一般在零点几毫秒，相对于前两个时间可以忽略不计。那么访问一次磁盘的时间，即一次磁盘IO的时间约等于5+4.17 = 9ms左右，听起来还挺不错的，但要知道一台500 -MIPS（Million Instructions Per Second）的机器每秒可以执行5亿条指令，因为指令依靠的是电的性质，换句话说执行一次IO的时间可以执行约450万条指令，数据库动辄十万百万乃至千万级数据，每次9毫秒的时间，显然是个灾难。下图是计算机硬件延迟的对比图，供大家参考：
```

![img](https://images2017.cnblogs.com/blog/1036857/201709/1036857-20170912010954844-413816327.png)

```
考虑到磁盘IO是非常高昂的操作，计算机操作系统做了一些优化，当一次IO时，不光把当前磁盘地址的数据，而是把相邻的数据也都读取到内存缓冲区内，因为局部预读性原理告诉我们，当计算机访问一个地址的数据的时候，与其相邻的数据也会很快被访问到。每一次IO读取的数据我们称之为一页(page)。具体一页有多大数据跟操作系统有关，一般为4k或8k，也就是我们读取一页内的数据时候，实际上才发生了一次IO，这个理论对于索引的数据结构设计非常有帮助。
```

磁盘每次预读的数据称之为一个block块，一个block块有4096个字节，对于MySQL每次读取4个block，Oracle每次读取两个block块。读取硬盘的IO操作事件非常长，比CPU执行指令的时间长很多，尽量的减少IO次数能够有效的提高读写数据的效率。

### 索引的数据结构

#### 树

树状图是一种数据结构，它是由n（n>=1）个有限结点组成一个具有层次关系的集合。它的根在上面，而叶在下方。

```
树的特点：
	1. 每个节点有零个或者多个子节点。
	2. 没有父节点的节点称为根节点。
	3. 每一个非根节点有且只有一个父节点。
	4. 除了根节点之外，每个子节点可以分为多个不相交的子树。
```

![img](https://img2018.cnblogs.com/blog/827651/201812/827651-20181217164400401-478241633.png)

```
根节点：A
父节点：A是B、C的父节点，B是D的父节点，C是E的父节点
叶子节点：D、E是叶子节点
树的深度/树的高度：高度为3
```

#### B树

B树：balance tree，平衡树。

定义：任意节点的子树的高度差都小于等于1。

```
通过算法对非平衡树进行改造，将其改造成平衡树。
```

#### B+树

在B树的基础上进行了改良。

改良的地方：

```
1. 分支节点和根节点都不再存储实际的数据，只存储数字和子节点的内存地址。通过这样的方式，让分支节点和根节点能存储更多的索引信息，降低了树的高度。最后，将所有的实际数据都存储在叶子节点中。

2. 在叶子节点之间加入双向的链式结构，方便在查询中的出现范围条件。即如果是查询条件，则找到第一个数据后，往左或往右都可以直接查询，不需要再去进行右边的查询。
```

B+树的性质：

```
1. 索引字段要尽可能地小：索引字段越小，则父节点和根节点存放的数据项越多，树的高度越低。

2. 索引的最左匹配特性：当b+树的数据项是复合的数据结构，比如(name,age,sex)的时候，b+数是按照从左到右的顺序来建立搜索树的，比如当(张三,20,F)这样的数据来检索的时候，b+树会优先比较name来确定下一步的所搜方向，如果name相同再依次比较age和sex，最后得到检索的数据；但当(20,F)这样的没有name的数据来的时候，b+树就不知道下一步该查哪个节点，因为建立搜索树的时候name就是第一个比较因子，必须要先根据name来搜索才能知道下一步去哪里查询。比如当(张三,F)这样的数据来检索时，b+树可以用name来指定搜索方向，但下一个字段age的缺失，所以只能把名字等于张三的数据都找到，然后再匹配性别是F的数据了， 这个是非常重要的性质，即索引的最左匹配特性。
```

#### MySQL的索引结构

MySQL当中所有的B+树索引的高度基本都控制在3层。

优点：

```
1. IO操作次数非常稳定。
2. 有利于进行范围查询。
```

#### 索引的效率

树的高度：

```
1. 对哪一列创建索引，尽量选择短的那一列作为索引，降低父节点存储的数据量。
2. 对区分度高的那一列创建索引，一般重复率超过10%的那一列，不适合创建索引（如性别）。
```

### 聚集索引与辅助索引

在数据库中的B+树索引可以分为聚焦索引（clustered index）和辅助索引（secondary index），相同点在于它们内部都是B+树的形式，即高度是平衡的，不同点在于叶子节点中存放的是否是一整行的数据。

#### 聚集索引

聚集索引：就是按照每张表的主键构造一颗B+树，同时叶子节点存放整张表的行记录数据，因此也将聚集索引的叶子节点称为数据页。聚集索引的特性决定了索引组织表中的数据也是索引的一部分，同B+树的数据结构一样，每个数据页都通过一个双向链表来进行连接。

```
1. 如果未定义主键，MySQL取第一个唯一索引（unique）而且不为空（NOT NULL）作为主键，innodb使用它作为聚集索引。

2. 如果没有这样的列，innodb会自己产生一个这样的ID值，六个字节，而且隐藏，作为聚集索引。

3. 由于实际的数据页只能按照一颗B+树进行排序，因此每张表只能拥有一个聚集索引。在多数情况下，查询优化器倾向于采用聚集索引，因为聚集索引能够直接在B+树的叶子节点上找到数据。此外，由于定义了数据的逻辑顺序，使得聚集索引能够特别快的访问针对范围的查询。
```

![img](https://images2018.cnblogs.com/blog/1036857/201711/1036857-20171125235128734-300879165.png)

好处：

```
1. 对主键的排序查找和范围查找速度非常快，叶子节点的数据就是用户所要查询的数据。

2. 范围查询（range query），即如果要查找主键某一范围内的数据，通过叶子节点的上层中间节点就可以得到页的范围，之后直接读取数据页即可。
```

#### 辅助索引

表中除了聚集索引外其他索引都是辅助索引，区别在于，辅助索引的叶子节点不包含行记录的全部数据。

辅助索引的叶子节点中除了包含键值以外，每个叶子节点中的索引行中还包含一个书签，该书签用来告诉innodb存储引擎去哪里可以找到与索引相对应的行数据。

由于innodb存储引擎是索引组织表，因此innodb存储引擎的辅助索引的书签就是相应行数据的聚焦索引键。

![img](https://images2018.cnblogs.com/blog/1036857/201711/1036857-20171126001518515-1178335624.png)

```
辅助索引的存在并不会影响数据在聚集索引中的组织，因此每张表上可以有多个辅助索引，但只能有一个聚集索引。当通过辅助索引来寻找数据时，innodb存储引擎会通过辅助索引找到聚集索引的主键，然后通过聚集索引来找到一个完整的行记录。

举例来说，如果在一棵高度为3的辅助索引树种查找数据，那需要对这个辅助索引树遍历3次找到指定主键，如果聚集索引树的高度同样为3，那么还需要对聚集索引树进行3次查找，最终找到一个完整的行数据所在的页，因此一共需要6次逻辑IO访问才能得到最终的一个数据页。
```

#### 聚集索引和非聚集索引的区别

```
聚集索引：
	1. 记录的索引顺序与物理顺序相同，因此更适合between，and和order by操作。
	2. 叶子节点直接对应数据，从中间级的索引页的索引行直接对应数据页。
	3. 每张表只能创建一个聚集索引。
非聚集索引：
	1. 索引顺序和物理顺序无关。
	2. 叶子节点不直接指向数据页。
	3. 每张表可以有多个非聚集索引，需要更多磁盘和内容，多个索引会影响insert和update的速度。
	
在innodb中聚集索引和辅助索引是并存的，而myisam中只有辅助索引。
```

### MySQL索引管理

#### 功能

```
1. 索引的功能就是加速查找

2. MySQL中的primary key，unique，联合唯一也都是索引，这些索引不仅加速查找，还有约束的功能。
```

#### MySQL的常用索引

```
普通索引index：加速查找

唯一索引：
	- 主键索引primary key：加速查找+约束（不为空，不能重复）
	- 唯一索引（unique）：加速查找+约束（不能重复）

联合索引：
	- primary key(id, name)：联合主键索引
	- unique(id, name)：联合唯一索引
	- index(id, name)：联合普通索引
```

#### 索引的两大类型hash与btree

```
创建上述索引的时候，分为两类：
	hash类型的索引：查询单条快，范围查询慢。
	btree类型的索引：B+树的层数越多，数据量指数级增长（innodb默认支持）。

不同的存储引擎支持的索引类型也不一样：
    InnoDB 支持事务，支持行级别锁定，支持 B-tree、Full-text 等索引，不支持 Hash 索引；
    MyISAM 不支持事务，支持表级别锁定，支持 B-tree、Full-text 等索引，不支持 Hash 索引；
    Memory 不支持事务，支持表级别锁定，支持 B-tree、Hash 等索引，不支持 Full-text 索引；
    NDB 支持事务，支持行级别锁定，支持 Hash 索引，不支持 B-tree、Full-text 等索引；
    Archive 不支持事务，支持表级别锁定，不支持 B-tree、Hash、Full-text 等索引；
```

#### 创建和删除索引的语法

```
方法1：
	create table 表名 (
	字段名1 数据类型 约束条件,
	字段名2 数据类型 约束条件...
	[unique | fulltext | spatial] index | key
	索引名 (字段名)
	);
	
方法2：
	create [unique | fulltext | spatial] index 索引名 on 表名 (字段名[(长度)], [asc | desc]);
	
方法3：
	alter table 表名 add [unique | fulltext | spatial] index 索引名 (字段名 [(长度)] [asc | desc]);
	
删除索引：drop index 索引名 on 表名;
```

```
创建索引时，创建的速度会很慢，而在索引创建完毕之后，以该字段为查询条件时，查询速度明显提升，但是占用的硬盘空间会随之增加。
```

#### 总结

```
1. 一定要为搜索条件的字段创建索引，比如select * from s1 where id=333;就需要为id加上索引。

2. 在表中已经有大量数据的情况下，创建索引就会很慢，并且占用硬盘空间，但是创建完之后，查询速度明显加快。

3. innodb表的索引会存放于ibd文件中，而myisam表的索引则会有单独的索引文件MYI。

myisam中的索引文件和数据文件是分离的，索引文件仅保存数据记录的地址，而在innodb中，表数据文件本身就是按照B+树组织的一个索引，这棵树的叶节点data域保存了完整的数据记录。这个索引的key是数据表的主键，因此innodb表数据文件本身就是主索引。
因为inndob的数据文件要按照主键聚集，所以innodb要求表必须要有主键（Myisam可以没有），如果没有显式定义，则mysql系统会自动选择一个可以唯一标识数据记录的列作为主键，如果不存在这种列，则mysql会自动为innodb表生成一个隐含字段作为主键，这字段的长度为6个字节，类型为长整型.
```



## 正确使用索引

### 索引未命中

```
1. 要查询的数据范围过大：
	1.1 <><=>=!=：这种比较运算符，尤其是!=直接崩。
	1.2 between A and B：范围小，差别不大，范围大，很慢。
	1.3 like：给字符串加索引，如果范围大，索引失效。'abc%'索引生效，'%abc'索引失效
	
2. 如果一列内容的区分度不高，索引失效：
	区分度公式：count(distinct conl)/count(*)，表示字段不重复的比例，一般高于0.9。
	区分度低的字段会导致B+树的高度很高，因而查询效率会较低。
	
3. 索引列不能在条件中参与计算，保证列“干净”：
	检索时会每次进行计算，在进行对比，因此效率很低。
	不能计算同时也包含了不能有函数。
	
4. 对两列内容进行条件查询：
	and：and条件两端会优先选择有索引的，并且树形结构更好的表来进行查询，and需要两个条件都成立才能完成where条件，先完成范围小的缩小后面条件的压力，从而加速查询。
	方法：制作联合索引。
	原则：将范围越小的字段往前放。
	or：or条件不会进行优化，只会根据条件从左到右一次筛选，如果or条件想要命中索引，需要条件中所有列都要有索引。
	方法：每个都进行单独索引。

5. 联合索引
	5.1 在联合索引中，如果使用了or条件索引就不能生效。
	5.2 在联合索引中，条件必须含有在创建索引的时候的第一个索引列。
	5.3 在整个条件中，从开始出现模糊匹配（>, <, like...）的那一刻，索引就失效。

6. 最左前缀匹配原则：对于组合索引MySQL会一直向右匹配直到遇到范围查询就停止匹配。
	(a, b, c)：a生效，ab生效，abc生效，但b不生效。
```

其他注意：

```
- 避免使用select *
- 使用count(*)
- 创建表时尽量使用 char 代替 varchar
- 表的字段顺序固定长度的字段优先
- 组合索引代替多个单列索引（由于mysql中每次只能使用一个索引，所以经常使用多个条件查询时更适合使用组合索引）
- 尽量使用短索引
- 使用连接（JOIN）来代替子查询(Sub-Queries)
- 连表时注意条件类型需一致
- 索引散列值（重复少）不适合建索引，例：性别不适合
```

### 联合索引与覆盖索引

#### 联合索引

联合索引是指对表上的多个列合起来做一个索引。联合索引的创建方法与单个索引的创建方法一样，不同之处仅仅在于有多个索引列。

```mysql
create index ind_a_b on t1(a, b);
```

![img](https://images2018.cnblogs.com/blog/1036857/201711/1036857-20171126004856453-1491949427.png)

```
(a, b)：查询a可以使用，因为明显a排序，但是查询b，明显不是排序的，使用不到索引。

联合索引在一个键相同的情况下，已经对第二个键进行了排序处理。
```

什么时候用联合索引？

只对a或者abc条件进行查询，而不会对bc进行单列的查询。

单列索引：选择一个区分度高的列创建索引，条件的范围尽量小，条件中的列不要参与计算，使用and作为条件的连接符。

or连接的条件：在满足单列索引的基础上，对or相关的所有列分别创建索引。



#### 覆盖索引

覆盖索引，即从辅助索引中就可以得到查询记录，而不需要查询聚集索引中的记录。

好处：辅助索引不包含整行记录的所有信息，故其大小要远小于聚集索引，同时查询的条件又都包含（也就是通过辅助查询就可以查到所有需要的信息），因此可以减少大量的IO操作。



### 执行计划

explain+sql语句：可以在执行sql语句之前就知道sql的执行情况。

```
情况1：
	30000条数据
	sql需要20s
	explain sql：并不会真正的执行sql，而是会给你列出一个执行计划
情况2：
	20条数据 ---- 300000
	explain sql：来推测sql的执行
```

### 索引合并

对两个字段分别创建索引，由于sql条件让两个索引同时生效，那么这个时候两个索引就成为合并索引。

### 慢查询优化步骤

```
0.先运行看看是否真的很慢，注意设置SQL_NO_CACHE
1.where条件单表查，锁定最小返回记录表。这句话的意思是把查询语句的where都应用到表中返回的记录数最小的表开始查起，单表每个字段分别查询，看哪个字段的区分度最高
2.explain查看执行计划，是否与1预期一致（从锁定记录较少的表开始查询）
3.order by limit 形式的sql语句让排序的表优先查
4.了解业务方使用场景
5.加索引时参照建索引的几大原则
6.观察结果，不符合预期继续从0分析
```



## 数据库备份

```
语法：
	mysqldump -h 服务器 -u用户名 -p密码 数据库名 > 备份文件地址/备份文件.sql
单库备份
    mysqldump -uroot -p123 db1 > db1.sql
    mysqldump -uroot -p123 db1 table1 table2 > db1-table1-table2.sql

多库备份
	mysqldump -uroot -p123 --databases db1 db2 mysql db3 > db1_db2_mysql_db3.sql

备份所有库
	mysqldump -uroot -p123 --all-databases > all.sql 
```

### 数据恢复

```
方法一：
	[root@egon backup]# mysql -uroot -p123 < /backup/all.sql

方法二：
    mysql> use db1;
    mysql> SET SQL_LOG_BIN=0;   #关闭二进制日志，只对当前session生效
    mysql> source /root/db1.sql
```



## 事务和锁

```
begin;  # 开启事务
select * from emp where id=1 for update;   # 查询id值，for update添加行锁
update emp set salary=1000 where id=1  # 完成更新
commit;  # 提交事务

# 其他的查询需要等待事务提交才能开始
```

## sql注入

```
--会注释掉--之后的sql语句（导致不安全）

后面如果需要做字符串的拼接使用：
cur.excute(sql, (username, password))  # 让sql自己拼接
```



## 作业

```mysql
# 1、查询男生、女生的人数；
select gender, count(sid) from student group by gender;  # count主键数量

# 2、查询姓“张”的学生名单；
select sname from student where sname like "张%";

# 3、课程平均分从高到低显示
desc：降序
asc：升序
select course_id, avg(num) from score group by course_id order by avg(num) desc;

# 4、查询有课程成绩小于60分的同学的学号、姓名；

内连接：select t1.sid, t1.sname, t2.course_id, t2.num from student as t1 inner join (select student_id, course_id, num from score) as t2 on t1.sid=t2.student_id where t2.num<60;

子查询：select t1.sid, t1.sname from student as t1 where t1.sid in (select student_id from score where num<60);

# 5、查询至少有一门课与学号为1的同学所学课程相同的同学的学号和姓名；
    # 1. 找到学号为1的同学学的课
    # 2. 找到课程id与1的结果相同的同学
    # 3. 打印学号和姓名

# 子查询
select course_id from score where student_id=1;
select student_id from score where course_id in (select course_id from score_table where student_id=1) and student_id!=1;
select sid, sname from student where sid in (select student_id from score where course_id in (select course_id from score where student_id=1) and student_id!=1);

# 连表查询
select t2.course_id from student as t1 inner join score as t2 on t1.sid=t2.student_id where t1.sid=1;
select t1.sid, t1.sname, t2.course_id, t2.num from (student as t1 inner join score as t2 on t1.sid=t2.student_id) where t2.course_id in (select course_id from score where student_id=1);

# 6、查询出只选修了一门课程的全部学生的学号和姓名；
    # 1. 根据学生分类，统计课程次数即成绩=1，拿到学号
    # 2. 根据学号，到学生表中查询学号和姓名
    
# 子查询
select student_id from score group by student_id having count(student_id)=1;
select sid, sname from student where sid = (select student_id from score group by student_id having count(student_id)=1);

# 连表查询
select t1.sid, t1.sname from student as t1 inner join score as t2 on t1.sid=t2.student_id group by t2.student_id having count(t2.student_id)=1; 

# 7、查询各科成绩最高和最低的分：以如下形式显示：课程ID，最高分，最低分；
select course_id as '课程ID', max(num) as '最高分', min(num) as '最低分' from score group by course_id;

# 8、查询课程编号“2”的成绩比课程编号“1”课程低的所有同学的学号、姓名；
	# 1. 根据学生分组，找到课程编号2成绩大于课程编号1成绩
	# 2. 将结果作为子查询去，student找

# 子查询
select sid, sname from student where sid in (select student_id from score where student_id in (select student_id from score where course_id=1) and course_id=2);

select sid, sname from student where sid in (select sid from score from (select num from score where student_id in (select student_id from score where student_id in (select student_id from score where course_id=1) and course_id=2) and course_id=1) > (select num from score where student_id in (select student_id from score where student_id in (select student_id from score where course_id=1) and course_id=2) and course_id=2));

# 连表查询


# 9、查询“生物”课程比“物理”课程成绩高的所有学生的学号；
# 这题跟上面一样，先获取编号，然后直接做就完事

select sid, sname from student where sid in (select student_id from score where student_id in (select student_id from score where course_id=(select cid from course where cname='生物')) and course_id=(select cid from course where cname='物理'));

# 10、查询平均成绩大于60分的同学的学号和平均成绩;
select student_id, avg(score) from score_table group by student_id having avg(score)>60;

# 11、查询所有同学的学号、姓名、选课数、总成绩；
select sid, sname, count(t2.course_id) as '选课数', sum(t2.score) as '总成绩' from student_table as t1 inner join (select student_id, course_id, score from score_table) as t2 on t1.sid = t2.student_id group by t2.student_id;

# 12、查询姓“李”的老师的个数；
select count(tid) from teacher_table where tname like '波%';

# 13、查询没学过“张磊老师”课的同学的学号、姓名；


# 14、查询学过“1”并且也学过编号“2”课程的同学的学号、姓名；
select student_id from score_table where course_id=1;
select student_id from score_table where course_id=2;
select sid, sname from student_table where sid = (select student_id from score_table where course_id=1) in (select student_id from score_table where course_id=2);

# 15、查询学过“李平老师”所教的所有课的同学的学号、姓名；

```





































