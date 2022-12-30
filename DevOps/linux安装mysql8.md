### 1、关闭防火墙

```bash
systemctl stop firewalld.service 
systemctl disable firewalld.service
```

### 2、删除

```bash
yum remove -y mariadb* 
rm -rf /var/lib/mysql 
```

### 3、查看centos的glibc版本号

```bash
ldd --version
```

### 4、下载Mysql8社区版本

下载地址

```bash
https://dev.mysql.com/downloads/mysql/8.0.html
```

解压

```bash
#如果后缀是.tar.gz 
tar -zxvf 文件名

#如果后缀是.tar.xz
tar -Jxvf 文件名
```

### 5、重命名

```bash
mv 原文件夹名 mysql8.0.31
```

### 6、添加环境变量

打开配置文件

```bash
vim /etc/profile
```

最后一行添加

```bash
export PATH=$PATH:/data/mysql8.0.31/bin
```

环境变量生效

```bash
source /etc/profile
```

设置执行权限

```bash
chmod 777 /data/mysql8.0.31/bin *
```

### 7、创建数据目录

```bash
mkdir /data/mysql8.0.31/mysql
```

### 8、创建my.cnf

```bash
vim /data/mysql8.0.31/my.cnf
```

写入以下内容

```ini
[mysql]
# 默认字符集
default-character-set=utf8mb4

[client]
port=3306
socket=/tmp/mysql.sock

[mysqld]
port=3306
server-id=3306
user= root
socket= /tmp/mysql.sock
bind-address=0.0.0.0 

# 安装目录
basedir=/data/mysql8.0.31

# 数据存放目录
datadir= /data/mysql8.0.31/data
log-bin= /data/mysql8.0.31/data/mysql-bin
innodb_data_home_dir=/data/mysql8.0.31/mysql
innodb_log_group_home_dir=/data/mysql8.0.31/mysql

#日志及进程数据的存放目录
log-error=/data/mysql8.0.31/data/mysql.log
pid-file=/data/mysql8.0.31/data/mysql.pid

# 服务端使用的字符集默认为8比特编码
character-set-server=utf8mb4

#表名不区分大小写
lower_case_table_names=1
autocommit=1

# 创建新表时将使用的默认存储引擎
default_storage_engine=InnoDB 

#binlog的格式也有三种：STATEMENT，ROW，MIXED。
binlog-format=mixed
#binlog过期清理时间
expire_logs_days=7
#binlog每个日志文件大小
max_binlog_size=1000m
#binlog缓存大小
binlog_cache_size=100m
max_binlog_cache_size=512m
#发生事务时非事务语句的缓存的大小
binlog_stmt_cache_size=100m
max_binlog_stmt_cache_size=100m


#优化设置-------------------------------------------
#启用admin_port，连接数爆满等紧急情况下给管理员留个后门
admin_address = 127.0.0.1
admin_port = 33062

#performance setttings
lock_wait_timeout = 3600
open_files_limit    = 65535
back_log = 1024
max_connections = 512
max_connect_errors = 1000000
table_open_cache = 1024
table_definition_cache = 1024
thread_stack = 512K
sort_buffer_size = 4M
join_buffer_size = 4M
read_buffer_size = 8M
read_rnd_buffer_size = 4M
bulk_insert_buffer_size = 64M
thread_cache_size = 768
interactive_timeout = 600
wait_timeout = 600
tmp_table_size = 32M
max_heap_table_size = 32M
```

### 9、初始化

```bash
mysqld --defaults-file=/data/mysql8.0.31/my.cnf --initialize-insecure
```

### 10、启动mysql

```bash
mysqld_safe --defaults-file=/data/mysql8.0.31/my.cnf
```

查看是否启动

```bash
ps -ef | grep mysql
```

### 11、注册为系统服务

修改mysql.server

```bash
vim /data/mysql8.0.31/support-files/mysql.server
```

修改以下项

```bash
basedir=/data/mysql8.0.31
datadir=/data/mysql8.0.31/data
# Set some defaults
mysqld_pid_file_path=/data/mysql8.0.31/data/mysql.pid
```

新建mysql.service

```bash
vim /usr/lib/systemd/system/mysql.service
```

填写如下内容

```bash
[Unit]
Description=mysql
After=syslog.target network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/data/mysql8.0.31/data/mysql.pid
ExecStart=/data/mysql8.0.31/support-files/mysql.server start
ExecReload=/data/mysql8.0.31/support-files/mysql.server restart
ExecStop=/data/mysql8.0.31/support-files/mysql.server stop
PrivateTmp=false

[Install]
WantedBy=multi-user.target

```

让服务生效

```bash
systemctl daemon-reload
```

启动服务

```bash
systemctl start mysql
```

### 12、登录

没有密码登录

```bash
mysql -uroot -p
```

修改密码

```bash
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';
FLUSH PRIVILEGES;
```

允许远程登录

```bash
use mysql;
update user set user.Host='%' where user.User='root';
flush privileges;
```

