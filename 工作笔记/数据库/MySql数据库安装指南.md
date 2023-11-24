---
title: MySql数据库安装指南
date: 2021-02-21 12:27:22
tags: 
	- Database
	- Mysql
description: Mysql一站式安装看这篇。
---

## 1.下载Mysql

## 2.初始化配置
使用下面的命令进行mysql数据库初始化
```bash
$ mysql_secure_installation
```
运行mysql_secure_installation会执行几个设置：  
--为root用户设置密码  
--删除匿名账号  
--取消root用户远程登录  
--删除test库和对test库的访问权限  
--刷新授权表使修改生效  

```bash
root@iZwz928lti88uknt38usc5Z:~# mysql_secure_installation

Securing the MySQL server deployment.

Connecting to MySQL using a blank password.
The 'validate_password' plugin is installed on the server.
The subsequent steps will run with the existing configuration
of the plugin.
Please set the password for root here.

New password:

Re-enter new password:

Estimated strength of the password: 100
Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : y
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
Success.


Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y
Success.

By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.


Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y
 - Dropping test database...
Success.

 - Removing privileges on test database...
Success.

Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
Success.

All done!

```

## 登录mysql
使用在```2.初始化配置```中为root用户设置的密码登录mysql，看到如下界面则代表登录成功。
```bash
root@iZwz928lti88uknt38usc5Z:~# mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 31
Server version: 5.7.33-0ubuntu0.18.04.1 (Ubuntu)

Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>

```
## 查看mysql server运行状态
```bash
systemctl status mysql
```
![执行结果](https://cdn.statically.io/gh/LBZZYZ/PicX@master/Blog/mysqlState.1v1675w98uf4.webp)

## 在Mysql中显示所有用户
```bash
SELECT User, Host, Password FROM mysql.user;(mysql version > 5.7)
select host,user,authentication_string from mysql.user;(mysql version <= 5.7>)
SELECT DISTINCT User FROM mysql.user;
```

## 修改mysql密码
plugin字段初始为unix_socket导致mysql_secure_installation设置密码不生效。  
https://blog.csdn.net/Fullspeeder/article/details/111184762

```bash
update mysql.user set authentication_string = password('Zxc1998325.'), plugin = 'mysql_native_password' where user = 'root';
flush privileges;
```

## 新建用户
https://www.cnblogs.com/sos-blue/p/6852945.html


## 配置远程访问
编辑 /etc/mysql/mysql.conf.d/mysqld.cnf 配置文件：

vim /etc/mysql/mysql.conf.d/mysqld.cnf

注释掉bind-address          = 127.0.0.1

保存退出，然后进入mysql数据库，执行授权命令：

mysql -u root -p

mysql> grant all on *.* to root@'%' identified by '你的密码' with grant option;

mysql> flush privileges;    # 刷新权限

mysql> exit

然后执行exit命令退出mysql服务，再执行如下命令重启mysql：

systemctl restart mysql

## 参考链接
https://www.cnblogs.com/opsprobe/p/9126864.html
https://blog.csdn.net/qq_32786873/article/details/78846008
https://www.cnblogs.com/xiaozi/p/10552100.html
https://developer.aliyun.com/article/415339
