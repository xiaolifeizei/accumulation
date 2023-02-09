### 1、当前连接数

```sql
select count(*) from v$session;
```

### 2、历史会话数的峰值

```sql
select sessions_current,sessions_highwater from v$license;
```

### 3、并发连接数

```sql
select count(*) from v$session where status='ACTIVE';
```

### 4、统计不同用户的连接数

```sql
select username,count(username) from v$session where username is not null group by username;
```

### 5、查看所有用户

```sql
select * from all_users;
```

### 6、查看哪些用户有sysdba或sysoper系统权限

查询时需要相应权限

```sql
select * from V$PWFILE_USERS;
```

### 7、当前数据库进程数

```sql
 select count(*) from v$process;
```

### 8、库允许的最大连接数

```sql
select value from v$parameter where name = 'processes' 
```

### 9、修改最大连接数

```sql
alter system set processes = 300 scope = spfile;
```

修改完成后重启数据库

```sql
shutdown immediate;
startup;
```

### 10、查看哪些用户正在使用数据库

```sql
SELECT osuser, a.username,cpu_time/executions/1000000||'s', sql_fulltext,machine from v$session a, v$sqlarea b where a.sql_address =b.address order by cpu_time/executions desc;
```

### 11、是否打开了闪回

```sql
select flashback_on from V$database;
```

### 12、打开闪回

1、登录

```bash
sqlplus /nolog
conn / as sysdba
```

2、查看是否配置了db_recover_file_dest

```sql
show parameter db_recovery;
```

3、如果没有配置需要先配置

创建目录

```bash
mkdir /flashback
```

分配权限

```bash
chown oracle:oinstall /flashback
```

4、修改闪回配置

指定恢复区大小

```sql
alter system set db_recovery_file_dest_size=30G scope=both;
```
指定闪回日志保留时间为2小时，即通过闪回操作，可以将数据库回退到前两小时内的任意时间点，默认值为1440，即24小时

```sql
alter system set db_flashback_retention_target=120; 
```

如果没有配置db_recover_file_dest执行如下语句

```sql
alter system set db_recovery_file_dest='/flashback'  scope=both;
```

5、重启数据库

```sql
shutdown immediate;
startup mount;
```

6、开启 archeve log

```sql
alter database archivelog;
```

7、开启闪回

```sql
alter database flashback on;
```

8、启动数据库到open状态

```sql
alter database open;
```

9、验证是否成功

```sql
select flashback_on from v$database;
```

看到YES表示成功

```bash
FLASHBACK_ON
------------------
YES
```

### 13、查看内存设置

```sql
show parameters target;
```

### 14、pfile、spfile

配置文件启动顺序如下：

```bash
spfileSID.ora---->spfile.ora---->initSID.ora---->init.ora（spfile优先于pfile）。
```

1、spfile默认路径

```bash
/product/11.2.0/dbhome_1/dbs
```

**2、oracle中有一个备份的初始化文件，可以通过它恢复spfile**

```bash
/opt/oracle/app/admin/${sid}/pfile/init.ora
```

恢复命令，先拷贝到dbs目录中

```bash
cp init.ora /product/11.2.0/dbhome_1/dbs/myorcal.ora
```

从初始化文件创建spfile

```sql
create spfile='/opt/oracle/app/product/11.2.0/dbhome_1/dbs/spfile${sid}.ora' from pfile='/opt/oracle/app/product/11.2.0/dbhome_1/dbs/myorcal.ora';
```

查看spfile文件位置

3、从spfile创建pfile

```sql
create pfile='/opt/oracle/app/product/11.2.0/dbhome_1/dbs/init${sid}.ora' from spfile;
```

4、启动数据库

```sql
startup;
```

### 15、设置内存

1、查看pfile文件，如果没有则应该先创建，参考15条

```sql
show parameter pfile;
```

2、修改pfile文件，

在pfile文件下添加如下配置

```bash
memory_max_target=4G
memory_target=4G
sga_target=0
pga_aggregate_target=0
```

3、关闭数据库

```sql
shutdown immediate;
```

4、创建spfile

```sql
create spfile from pfile='/opt/oracle/app/product/11.2.0/dbhome_1/dbs/initorcl.ora';
```

5、启动数据库

```sql
startup
```

5、如果启动报错：ORA-00845: MEMORY_TARGET not supported on this system

MEMORY_MAX_TARGET 的设置不能超过 /dev/shm 的大小。

查看shm内存大小

```bash
df -h | grep shm
```

修改

```bash
vi /etc/fstab
```

在最后添加一行

```bash
tmpfs /dev/shm tmpfs defaults,size=4G 0 0
```

重新挂载

```bash
mount -o remount /dev/shm
```

   如果在docker中可能会提示：mount: permission denied

- 先执行查看容器的id

  ```bash
  docker inspect oracle11g
  ```

- 修改配置文件

  ```bash
  vi /var/lib/docker/containers/id/hostconfig.json
  ```

- 修改Privileged属性为true

- 重启docker服务

  ```bash
  systemctl restart docker
  ```

5、查询是否生效

```sql
show parameters target;
```

