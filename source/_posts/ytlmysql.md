---
title: ytlmysql
date: 2021-07-25 17:20:59
tags: MySQL
categories: MySQL
---

## ytlmysql
### 一.连接MySQL服务(MySQL数据库)的指令,查询电脑ip命令(ipconfig)
mysql - h 主机IP -P 端口 -u 用户名 -p密码
提醒:
(1)-p密码不要有空格,
(2)-p后面没有输入密码,回车会要求输入密码
(3)如果没有写-h 主机ip,默认就是主机
(4)如果没有写-p 端口,默认就是3306
(5)在实际工作中,3306一般会修改
(6)登入之前确保MySQL服务是启动状态 net start mysql 启动服务 net stop mysql 停止服务

### 二.查看数据:
```mysql
show databases
use mysql
show tables;
select * from user;  # 查询user表
```

### 三.创建数据库:
create database db02;  # 创建db02数据库,并设置自增，设置id为主键
use db02
create table users (id int primary key auto_increment not null, name varchar(255), address varchar(255));  # 创建表格
insert into users values(1,'tom','bj');  # 插入数据
insert into users values(2,'jack','sh');  # 插入数据
insert into users values(3,'shunping','gz');  # 插入数据
select * from users;  # 查询users表格数据
指令创建表,格式如下:注意(字体变蓝说明是个关键字,我们应该加个反引号``这个引起来)
```MySQL
CREATE TABLE `表名`(
指定列名1 指定列类型(字段类型),
id int primary key auto_increment not null,
`password` VARCHAR(255),
`birthday` DATE)
CHARACTER SET utf8 COLLATE utf8_bin ENGINE INNODB;
character set:字符集,如不指定测为所在数据库字符集,].
collate:校对规则,如不指定则为所在数据库校对规则.
engine:存储引擎,涉及内容较多.
id        整形(int)
name      字符串(varchar(225))
password  字符串(varchar(255))
birthday  日期(date)
```
```MySQL
CREATE TABLE `user02`(
	id INT primary key auto_increment not null,
	`name` VARCHAR(255),
	`password` VARCHAR(255),
	`birthday` DATE)
	CHARACTER SET utf8 COLLATE utf8_bin ENGINE INNODB;
```
删除主键
```MySQL
ALTER TABLE `table_name` DROP PRIMARY KEY;
```
在已有得表里添加主键
```MySQL
ALTER TABLE `table_name` ADD PRIMARY KEY(id);
```
添加自动递增AUTO_INCREMENT
```MySQL
alter table table_name add column `id` bigint(20) NOT NULL PRIMARY KEY AUTO_INCREMENT COMMENT '自增id' first;
```
增加两个字段
```MySQL
alter table table_name add age int,add address varchar(11);
```
在指定的一个字段后面添加一个字段
```MySQL
alert table 表名  add 新列名 类型 comment '添加备注' after  要跟随的字段名 (可写可不写)
alter table user add sex int comment '性别' after nickname;
```
删除字段
```MySQL
alter table table_name drop column id;
```
修改字段
```MySQL
alter table table_name modify `字段` varchar(25);  # 修改一个字段的类型
alter table table_name change `字段` `字段新名` int;  # 修改一个字段的名称，此时一定要重新指定该字段的类型
```

### 四.备份恢复数据库或者表:
(1)备份数据库(注意:在DOS执行,后面不带分号)
```cmd
mysqldump -u 用户名 -p -B 数据库1 数据库2 数据库n > d:\\path文件名.sql
```
(2)备份库的表:(注意:备份表不要-B)
```cmd
mysqldump -u 用户名 -p密码 数据库名 表名1 表名2 表名n > d:\\path文件名.sql
```
(3)恢复数据库(注意:进入mysql里面在执行,先创建新的数据库名,在在这个库名下面备份数据)
```MySQL
create database 新库名;
use 新库名;
Source d:\\path文件名.sql
```
[误删或者吴修改日志找回链接](https://www.cnblogs.com/gslgb/p/13210800.html)

### 五.删除数据库里面的表:
```MySQL
drop table 表名:
```

### 六.删除数据库(注意:进入mysql在执行,慎重使用.)
```MySQL
drop database 库名;
```

### 七.数据库的增删改查
#### 1.增
```mysql
1、向所有列插入数据--into关键字可以省略
insert    into    表名  values（列值1，列值2，……）
insert    into  studentinfo  values（‘004’，‘张鑫’，19，‘北京海淀’）
2、向指定列插入数据--into关键字可以省略
，不提供值的列一定是可以为空的列（null），
列名的顺序可以与表结构中的顺序不同，只要列值的顺序与之对应就行
insert    studentinfo（姓名，学号）  values（‘王强’，‘006’）
insert  into  表名（列名1，列名2，……） values（列值1，列值2，……）
insert    into  studentinfo（学号，姓名）  values（‘005’，‘李清’
identity--相同：对于标识列，因为是自动增长的，所以不能指定值
default--系统设定值：具有缺省值的列，可以使用default关键字来替代插入的数据
```
#### 2.删
```mysql
删除表中数据--from关键字可以省略，如果不指定where条件，将把表中所有记录都删除掉（很危险！），
如果使用where指定条件，则满足条件的记录被删除
不能只删除半行或某几列数据
delete-删除   from   Tab1
delete   book  where  bid='s0000'
truncate-删除所有   table 表名--不能指定条件，只能删除所有数据，所以功能等价于“delate  表名”的语句 
优点：速度更快，使用的系统资源和事务日志资源更少
缺点：不会将删除每一条记录的操作记录到日志中，所以一旦删除，则无法通过日志恢复删除的数据
drop-删除    database-数据库 表名--删除数据库名
drop table-表 表名--删除表结构
```
#### 3.改
```mysql
update--更新 表名 set--日落 列名1=该列新值，列名2=该列新值，......where 条件
update 表名 set salary=salary+100--表述每行工资都加100
update book set pub='新华出版社'   where    Bname='娱乐天地‘--更新指定的记录
update book set author2='江泽民',numinput=100,numstore=100 where author1='邓小平'--同时更新多个字段
```
#### 4.查
```mysql
select * from 表名--查询所有记录
select 列名1，列名2，…… from 表名--查询指定的列，列的顺序可以与表结构的顺序不同，列之间使用逗号分隔
select Cid, address, Cname, Phone  from  customer

select 列名1 as 列别名1，列名2 as 列别名2，…… from 表名--重新为列指定列名，as关键字可以省略
select Bname as 书名,Author1 as 作者,Pub as 出版社 from book

select cid 学号,cname 姓名, address ,phone as 电话 from customer

select 列别名1=列名1，列别名2=列名2 ，…… from 表名--使用等于号重新为列指定列名
select 书名=bname,书号=bid,pub from book

select 列名1 +列名1 as 列别名，…… from 表名--可以把多列合并成一列显示

select 列别名=列名1 +列名1 ,…… from 表名
select 列名1 ，列名2 ，常量值 as 列名 from 表名--使用常量
select * from 表名 order by age asc--升序，salary desc--降序
select * from 表名 where address like '深圳%'

在mysql数据库中要查询前10条数据，不再使用top n语句，top n语句主要用在sqlserver数据库系统中，
在mysql中一般使用 select * from 表名 limit 0,10 order by 字段 desc,
如在学生表中查询年龄比较大的前10个学生，则这样写，
select * from 学生表 limit 0,10 order by 年龄 desc。（一般使用这个 在后面加 limit 0,10）
select top 5 * from customer （这个在sqlserver数据库中使用，在cmd使用无效）
select * from table_name limit 0,10 (0代表从第几行开始，10代表显示多少行)

select * from book where numstore between 20 and 30 
select * from book where id in （'a0001','a0002'）
合函数方便进行数据统计
聚合函数不能在 where 中使用 
常用聚合函数 
count(): 查询总记录数 ：查询学生总数 select count(*) from students;
max(): 查询最大值 ：查询女生的最大年龄 select max(age) from students where sex='女'; 
min(): 查询最小值 ：查询1班的最小年龄 select min(age) from students;
sum(): 求和 ：查询北京学生的年龄总和 select sum(age) from students where hometown='北京';
avg(): 求平均值：查询女生的平均年龄 select avg(age) from students where sex='女' 
```