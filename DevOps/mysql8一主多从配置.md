### 1、主库配置

```ini
[mysqld]
#主从设置-------------------------------------------------------
#mysql服务ID，保证整个集群环境中唯一，取值范围: 1 - 2的32次方-1，默认为1
server-id=1
#是否只读,1 代表只读,0 代表读写
read-only=0
super-read-only=0
```

### 2、重启mysql服务

```bash
systemctl stop mysql
systemctl start mysql
```

### 3、登录主库

```bash
mysql -uroot -p
```

### 4、查看主库状态

```sql
show master status;

+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000011 |      157 |              |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)
```

File：从哪个日志文件开始推送日志文件
Position：从哪个位置开始推送日志
Binlog_ignore_db：指定不需要同步的数据库

### 5、从库配置

```ini
[mysqld]
#主从设置-------------------------------------------------------
#mysql服务ID，保证整个集群环境中唯一，取值范围: 1 - 2的32次方-1，默认为1
server-id=2
#是否只读,1 代表只读,0 代表读写
read-only=1
super-read-only=1
```

### 6、重启从库

```bash
systemctl stop mysql
systemctl start mysql
```

### 7、从库登录mysql

```bash
mysql -uroot -p
```

### 8、设置从库

```sql
mysql >change master to  master_host="192.168.230.200",master_port=3306,master_user="root",master_password="root",master_log_file="mysql-bin.000011",master_log_pos=157;
```

### 9、启动从库

```sql
mysql>start slave;
```

### 10、查看从库状态

````sql
mysql >show slave status \G;

*************************** 1. row ***************************
					....
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes

````

### 11、重做同步

#### 11.1、主库设置只读

```ini
set global super_read_only=1;
set global read_only=1;
```

#### 11.2、导出数据库

```bash
$ mysqldump -uroot -p --opt -R 数据库 > /data/bak.sql
```

#### 11.3、复制到从库

```bash
scp /data/bak.sql root@192.168.230.202:/data/bak.sql
```

#### 11.4、登录到从库

```bash
mysql -h 192.168.230.201 -P 3306 -u root -p
```

#### 11.4、停止从库

```sql
mysql> stop slave;
```

#### 11.5、导入数据库

```sql
mysql> source /data/bak.sql
```

#### 11.6、重置同步

```sql
reset slave;
```

#### 11.7、重新设置同步节点

在主库上查看状态

```sql
show master status;

+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000011 |     2156 |              |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)
```

在从库上执行

```sql
change master to  master_host="192.168.230.200",master_port=3306,master_user="root",master_password="root",master_log_file="mysql-bin.000011",master_log_pos=2156;
```

在从库上开启同步

```sql
start slave;
```

在从库上查看同步状态

```sql
show slave status \G;
```

#### 11.8、主库恢复读写

```sql
set global super_read_only=0;
set global read_only=0;
```

### 12、备库升级为主库

```sql
stop slave;
SET global read_only=0;
set global super_read_only=0;
reset slave all;
```

### 13、主库降为备库

```sql
SET global read_only=1;
set global super_read_only=1;

change master to  master_host="192.168.230.201",master_port=3306,master_user="root",master_password="root",master_log_file="mysql-bin.000005",master_log_pos=4714;

start slave;
show slave status \G;
```

