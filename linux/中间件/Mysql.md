# mysql

## 安装

1、更新软件包列表，升级已安装的软件包

```bash
sudo apt update
sudo apt upgrade
```

2、安装MySQL服务器

```bash
sudo apt install mysql-server -y
```

3、MySQL 安全配置

```bash
Remove anonymous users? (Press y|Y for Yes, any other key for No) : y # 移除匿名用户
Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y # 禁止root远程登录
Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y # 移除test数据库和访问权限
Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y # 刷新权限表

Success.

All done! 
```

## 管理

```bash
# 启动 MySQL 服务
sudo systemctl start mysql

# 停止 MySQL 服务
sudo systemctl stop mysql

# 重启 MySQL 服务
sudo systemctl restart mysql

# 查看 MySQL 服务状态
sudo systemctl status mysql

# 查看 MySQL 服务的错误信息
sudo tail -f /var/log/mysql/error.log
```

## MySQL 远程连接

1、修改 MySQL 配置文件

MySQL 默认只允许本地连接（127.0.0.1），需要修改配置文件让它可以监听所有网络接口。

打开配置文件

```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf 
```

找到并修改以下行：

```bash
# 找到这一行（通常在文件中间位置）
bind-address = 127.0.0.1

# 改为：
bind-address = 0.0.0.0
```

或者可以注释掉这一行（在前面加 `#`）：

```bash
# bind-address = 127.0.0.1
```

完整内容

```
# The MySQL database server configuration file.
#
# One can use all long options that the program supports.
# Run program with --help to get a list of available options and with
# --print-defaults to see which it would actually understand and use.
#
# For explanations see
# http://dev.mysql.com/doc/mysql/en/server-system-variables.html

# Here is entries for some specific programs
# The following values assume you have at least 32M ram

[mysqld]
#
# * Basic Settings
#
user            = mysql
# pid-file      = /var/run/mysqld/mysqld.pid
# socket        = /var/run/mysqld/mysqld.sock
# port          = 3306
# datadir       = /var/lib/mysql


# If MySQL is running as a replication slave, this should be
# changed. Ref https://dev.mysql.com/doc/refman/8.4/en/server-system-variables.html#sysvar_tmp>
# tmpdir                = /tmp
#
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
# a+La+La+Lbind-address         = 127.0.0.1
mysqlx-bind-address     = 127.0.0.1
#
# * Fine Tuning
#
key_buffer_size         = 16M
# max_allowed_packet    = 64M
# thread_stack          = 256K

# thread_cache_size       = -1

# This replaces the startup script and checks MyISAM tables if needed
# the first time they are touched
myisam-recover-options  = BACKUP
```

保存并退出：

- 按 `Ctrl + X`
- 按 `Y` 确认保存
- 按 `Enter` 退出

2、重启 MySQL 服务使配置生效：

```bash
sudo systemctl restart mysql
```

### 创建远程访问用户

为了安全，不建议使用 root 账户进行远程连接，应该创建一个专门用于远程访问的用户。

1、登录 MySQL：

```bash
sudo mysql -u root -p
```

创建远程用户并授权

**方式一：允许从任何 IP 连接**（方便但安全性较低）：

```sql
CREATE USER 'myuser'@'%' IDENTIFIED BY '您的强密码'; # 创建用户 myuser，允许从任何 IP 连接
GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'%' WITH GRANT OPTION; # 授权予 myuser 所有数据库的所有权限
FLUSH PRIVILEGES; # 刷新权限表
```

**方式二：仅允许特定 IP 连接**（更安全，推荐）：

```sql
-- 将 '192.168.1.100' 替换为您的客户端 IP
CREATE USER 'myuser'@'192.168.1.100' IDENTIFIED BY '您的强密码'; # 创建用户 myuser，仅允许从指定 IP 连接
GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'192.168.1.100' WITH GRANT OPTION; # 授权予 myuser 所有数据库的所有权限
FLUSH PRIVILEGES; # 刷新权限表
```

**方式三：限制只能访问特定数据库**（最安全，推荐）：

```sql
CREATE USER 'myuser'@'%' IDENTIFIED BY '您的强密码'; # 创建用户 myuser，仅允许访问 mydatabase 数据库
GRANT ALL PRIVILEGES ON mydatabase.* TO 'myuser'@'%' WITH GRANT OPTION; # 授权予 myuser mydatabase 数据库的所有权限
FLUSH PRIVILEGES; # 刷新权限表
```

查看用户是否创建成功

```sql
SELECT User, Host FROM mysql.user; # 查看所有用户和主机
```

退出 MySQL

```sql
EXIT; # 退出 MySQL
``````

### 配置防火墙（如果已启用）

检查防火墙状态

```bash
sudo ufw status # 查看防火墙状态
```

开放端口

```bash
# 先确保 SSH 端口开放，防止被锁在外面
sudo ufw allow 22/tcp

# 开放 MySQL 端口（如果需要远程连接）
sudo ufw allow 3306/tcp

# 启用防火墙
sudo ufw enable
```

成功

```bash
状态：活动

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere
3306/tcp                   ALLOW       Anywhere
```

### 测试远程连接

在远程客户端上执行以下命令测试连接：

```bash
mysql -u myuser -p -h 服务器IP地址 # 连接 MySQL 服务器，用户名 myuser，密码 mypassword，服务器 IP 地址
```

输入密码后，如果能成功进入 MySQL 命令行，说明远程连接配置成功！🎉

```bash
C:\Users\zhaohui>mysql -u tom -p -h 192.168.144.129
Enter password: *********

Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 19
Server version: 8.4.10-0ubuntu0.26.04.1 (Ubuntu)

Copyright (c) 2000, 2025, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```

