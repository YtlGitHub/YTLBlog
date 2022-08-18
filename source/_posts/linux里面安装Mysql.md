---
title: linux里面安装Mysql
date: 2022-08-18 15:37:13
tags: linux
categories: linux
---
熟悉linux
虚拟机、镜像iso下载地址及说明：https://blog.csdn.net/m0_61481871/article/details/123493436
## 在linux里面安装mysql

#### 1.检查linux版本
```linux
cat /etc/centos-release
```

#### 2.检查网络是否ping通
```linux
ping www.baidu.com
```
#### 3.mysql官网找到对应得rpm包复制链接
```linux
https://dev.mysql.com/get/mysql80-community-release-el7-6.noarch.rpm
```
说明：提示权限不够得话切到root权限。# sudo -s #输入密码进入root用户模式，此时仍在原目录下，注意不要执行su -root命令，会进入root目录;
#### 3.liunx下载rpm包
```linux
wget https://dev.mysql.com/get/mysql80-community-release-el7-6.noarch.rpm
```
说明：wget工具如果没有得话 下载：yum install wget
#### 4.安装rpm
```linux
rpm -ivh mysql80-community-release-el7-6
```
#### 5.安装mysql
```linux
yum install mysql-community-server
```
说明：如果提示获取 GPG 密钥失败：就修改：
```linux
vim /etc/yum.repos.d/mysql-community.repo 
```
把gpgcheck=1改为gpgcheck=0 
shift+:wq!保存退出
#### 6.设置mysql
```linux
mysql_secure_installation
```
说明：报错不急 先启动mysql如下第7不
#### 7.启动mysql服务
```linux
service mysqld restart
```
#### 8.查看sql自动生成得初始密码
```linux
grep 'passwor' /var/log/mysqld.log
```
#### 9.修改密码
```linux
alter user 'root'@'localhost' identified by '532689.Aawer';
```
#### 10.退出
```linux
exit
```
#### 11.重新进
```linux
mysql -uroot -p
mysql -h 需要连接得ip地址 -P 端口号一般是3306 -u 用户名 -p 密码
```
#### 12.查看信息
```linux
help 'create database';
```



