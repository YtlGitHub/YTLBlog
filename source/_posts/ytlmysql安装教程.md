---
title: ytlmysql安装教程
date: 2021-07-24 17:28:47
tags: MySQL
categories: MySQL
---

## ytlmysql安装
### 一.下载MySQL
1.点击[这里](https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.19-winx64.zip)下载mysql-5.7.19-winx64.zip压缩包
2.下载完之后解压到D:\ytlmysql\mysql-5.7.19-winx64目录下
### 二.配置用户环境变量
1.右击此电脑选择属性点击高级系统设置，点击高级最下面的环境变量
2.选择用户变量把路径配置到path：D:\ytlmysql\mysql-5.7.19-winx64\bin里面，之后点击确定关闭即可
### 三.在本机配置MySQL
1.新建My.ini文件放到D:\ytlmysql\mysql-5.7.19-winx64\目录下
内容结构如下复制到My.ini文件里面就行
```My.ini
[client]
port=3306  # 端口
default-character-set=utf8  # 编码格式
[mysqld]
# 设置为自己的安装目录
basedir=D:\ytlmysql\mysql-5.7.19-winx64\
# 设置为MySQL的数据目录,这个目录是系统创建
datadir=D:\ytlmysql\mysql-5.7.19-winx64\data\
port=3306
character_set_server=utf8
# 开启日志
server_id = 1
log_bin = D:\ytlmysql\mysql-5.7.19-winx64\mysql-bin
# 跳过安全检查
# skip-grant-tables
```
2.以管理员的身份打开cmd,并切换到 cd /d D:\ytlmysql\mysql-5.7.19-winx64\bin\ 目录下,执行如下命令
```shell
mysqld -install
```
如是新装的win10系统执行这个命令会报错，需另外安装[vcredist_x64.exe](https://github.com/YtlGitHub/YtlMySQL/blob/main/%E6%95%B0%E6%8D%AE%E5%BA%93%E5%AE%89%E8%A3%85%E6%95%99%E7%A8%8B/%E5%AE%89%E8%A3%85mysql%E5%87%BA%E9%94%99%E6%97%B6%E5%AE%89%E8%A3%85%E5%90%8E%E7%BD%AE%E5%B8%A6dll/vcredist_x64.exe)插件，在执行这个命令就成功了
3.初始化数据库
```shell
mysqld --initialize-insecure --user=mysql
```
4.启动数据库
```shell
net start mysql
```
5.停止数据库,这个先不关最后不用了在关
```shell
net stop mysql
```
### 四.修改root密码
1.进入mysql管理终端:mysql -u root -p (当前root用户密码为空),不需要输入密码直接回车就可以进入mysql
```shell
mysql -u root -p
```
2.修改root密码
```shell
use mysql;
update user set authentication_string=password("ytl")where user="root" and Host="localhost";
flush privileges;
```
上面语句就行修改root用户的密码为ytl
注意在后面需要带';'分号,回车执行指令
在执行flush privileges;刷新权限
最后退出quit,注意这里只是退出终端,但是MySQL服务是还在运行的.
3.回到D:\ytlmysql\mysql-5.7.19-winx64\目录下打开my.ini文件注销里面的# skip-grant-tables,注意修改完之后记得要重新启动才能生效.
4.注销后再执行net stop mysql停止MySQL,在执行net start mysql启动MySQL服务
5.安装完成
### 五.进入本地mysql
现在重新进入MySQL,输入正确的用户和密码
```shell
mysql -u root -p
```
### 六.修改权限让其他用户也可以连接你的mysql
#### 1.改表法
可能是你的帐号不允许从远程登陆，只能在localhost。这个时候只要在localhost的那台电脑，登入mysql后，更改 "mysql" 数据库里的 "user" 表里的 "host" 项，从"localhost"改称"%"
```shell
mysql -u root -p
mysql>use mysql;
mysql>update user set host = '%' where user = 'root';
mysql>select host, user from user;
```
#### 2.授权法
(1)例如，你想用户'ytluser'使用密码'ytl'从任何主机连接到mysql服务器的话。
```shell
GRANT ALL PRIVILEGES ON *.* TO 'ytluser'@'%' IDENTIFIED BY 'ytl' WITH GRANT OPTION;
FLUSH PRIVILEGES;
如果是新版mysql授权用户时报错
问题：当我们新装的mysql，输入创建用户的命令后，提示语法错误，报错如下：
mysql> grant all privileges on *.* to xma@'%' identified by 'myslag123!@#';
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'identified by 'myslag123!@#'' at line 1
原因：因为新版的的mysql版本已经将创建用户和赋予权限的操作分开了，我们可以分两步进行操作。
解决办法
1.创建用户
create user 'ytluser'@'%' identified by 'ytl';
2.赋予授权
grant all privileges on *.* to 'ytl'@'%';<br>
3.刷新权限
FLUSH PRIVILEGES;
```
(2)如果你想允许用户ytluser从ip为10.127.56.177的主机连接到mysql服务器，并使用ytl作为密码
```shell
GRANT ALL PRIVILEGES ON *.* TO 'ytluser'@'10.127.56.177' IDENTIFIED BY 'ytl' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

(3)如果你想允许用户ytluser从ip为192.168.1.6的主机连接到mysql服务器的db(库名)数据库，并使用ytl作为密码
```shell
GRANT ALL PRIVILEGES ON db.* TO 'ytluser'@'192.168.1.3' IDENTIFIED BY 'ytl' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```
