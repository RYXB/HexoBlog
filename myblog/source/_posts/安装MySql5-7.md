---
title: 安装MySql5.7
date: 2022-03-17 21:10:53
tags:
---

# 安装MySQL 5.7

下载安装包上传到服务器（略过）

安装包位置

/opt/soft/mysql-5.7.35-el7-x86_64.tar.gz

解压位置

/usr/local/mysql

创建mysql 用户组和用户

```shell
groupadd mysql
useradd -r -g mysql mysql
```

更改mysql 目录下所有文件夹所属的用户组和用户，以及权限

```shell
chown -R mysql:mysql /usr/local/mysql 
chmod -R 755 /usr/local/mysql
```

创建mysql相关目录

```shell
mkdir -p /data/mysql/{data,logs,tmp} 
mkdir -p /data/mysql/{data,logs,tmp}
```

创建mysql配置文件my.cnf

```shell
[client]
port = 3306
socket = /data/mysql/tmp/mysql.sock
[mysqld]
user = mysql
basedir = /usr/local/mysql
datadir = /data/mysql/data 
port = 3306 
socket = /data/mysql/tmp/mysql.sock
pid-file = /data/mysql/tmp/mysqld.pid
tmpdir = /data/mysql/tmp 
skip_name_resolve = 1
symbolic-links=0
max_connections = 2000
group_concat_max_len = 1024000
sql_mode = NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
lower_case_table_names = 1
log_timestamps=SYSTEM
character-set-server = utf8
interactive_timeout = 1800 
wait_timeout = 1800
max_allowed_packet = 32M
binlog_cache_size = 4M
sort_buffer_size = 2M
read_buffer_size = 4M
join_buffer_size = 4M
tmp_table_size = 96M
max_heap_table_size = 96M
max_length_for_sort_data = 8096
#logs
server-id = 1003306
log-error = /data/mysql/logs/error.log
slow_query_log = 1
slow_query_log_file = /data/mysql/logs/slow.log
long_query_time = 3
log-bin = /data/mysql/logs/binlog
binlog_format = row
expire_logs_days = 15
log_bin_trust_function_creators = 1
relay-log = /data/mysql/logs/relay-bin
relay-log-recovery = 1 
relay_log_purge = 1 
#innodb 
innodb_file_per_table = 1
innodb_log_buffer_size = 16M
innodb_log_file_size = 256M
innodb_log_files_in_group = 2
innodb_io_capacity = 2000
innodb_io_capacity_max = 4000
innodb_flush_neighbors = 0
innodb_flush_method = O_DIRECT
innodb_autoinc_lock_mode = 2
innodb_read_io_threads = 8
innodb_write_io_threads = 8
innodb_buffer_pool_size = 2G
```

配置mysql.server

```shell
cd /usr/local/mysql/support-files 
cp mysql.server /etc/init.d/mysql 
vi /etc/init.d/mysql 
# 修改目录位置 
basedir=/usr/local/mysql 
datadir=/data/mysql/data 
# 注册开机启动服务
 chkconfig --add mysql 
 chkconfig --list
```

添加环境变量

```shell
echo "PATH=$PATH:/usr/local/mysql/bin " >> /etc/profile  
source /etc/profile
```

初始化mysql

```shell
/usr/local/mysql/bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/data/mysql/data 
# 临时密码保存在errlog中  
# 获取临时密码 
more /data/mysql/logs/error.log |grep password
```

启动mysql服务，并修改密码

```shell
# 启动mysql服务 
service mysql start 
# 使用初始密码登录mysql服务 并修改密码 
mysql -uroot -p 
alter user 'root'@'localhost' identified by 'Fb@54321'; 
flush privileges;
```

