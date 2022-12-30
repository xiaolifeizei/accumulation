### 1、mycat下载

官网可能受到DNS污染导致无法访问,下载服务失效,导致安装下载不便

可以修改操作系统的host文件添加下面行,绕过DNS解析

```
210.51.26.184 mycat.org.cn www.mycat.org.cn dl.mycat.org.cn
```

下载地址：

http://dl.mycat.org.cn/2.0/

### 2、配置

下载install-template.zip和jar包，并把jar包放到install-template的lib目录下，上传到服务器的data目录下

### 3、添加mycat\bin目录下文件的执行权限

```bash
chmod 777 *
```

### 4、安装jdk

解压缩

```bash
tar -zxvf java8.tar.gz
```

配置环境变量

```bash
vim /etc/profile 
```

添加到最后

```ini
JAVA_HOME=/data/jdk1.8.0_351
PATH=$JAVA_HOME/bin:$PATH 
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tool.jar
export JAVA_HOME CLASSPATH PATH
```

让配置文件生效

```bash
source /etc/profile
```

测试是否成功

```bash
java -version
```

### 5、修改mycat中的prototype配置

```bash
vim /data/mycat/conf/datasources/prototypeDs.datasource.json 
```

```json
{
        "dbType":"mysql",
        "idleTimeout":60000,
        "initSqls":[],
        "initSqlsGetConnection":true,
        "instanceType":"READ_WRITE",
        "maxCon":1000,
        "maxConnectTimeout":3000,
        "maxRetryCount":5,
        "minCon":1,
        "name":"prototypeDs",
        "password":"root",
        "type":"JDBC",
        "url":"jdbc:mysql://192.168.230.201:3306/mysql?useUnicode=true&serverTimezone=Asia/Shanghai&characterEncoding=UTF-8",
        "user":"root",
        "weight":0
}
```

### 6、修改mycat密码

```bash
vim /data/mycat/conf/users/root.user.json
```

修改password

```json
{
        "dialect":"mysql",
        "ip":null,
        "password":"123456",
        "transactionType":"proxy",
        "username":"root"
}
```

### 7、启动mycat

```bash
/data/mycat/bin/./mycat start
```

如果查看启动结果

```bash
/data/mycat/bin/./mycat console
```

### 8、登录mycat并创建数据库

```bash
mysql -uroot -p123456 -P 8066 -h 127.0.0.1 
```

创建数据库

```sql
create database test;
```

### 9、mycat中修改schemas，添加集群名称

```bash
vim /data/mycat/conf/schemas/test.schema.json
```

指定集群信息：`"targetName":"pt",`

```json
{
        "customTables":{},
        "globalTables":{},
        "normalProcedures":{},
        "normalTables":{},
        "schemaName":"test",
    	"targetName":"pt",
        "shardingTables":{},
        "views":{}
}
```

### 10、mycat中添加数据源

使用注解方式添加，添加主库（读写）

```sql
/*+ mycat:createDataSource{"name":"master","url":"jdbc:mysql://192.168.230.201:3306/mysql?useUnicode=true&serverTimezone=Asia/Shanghai&characterEncoding=UTF-8","user":"root","password":"root" } */;
```

添加从库1（只读）

```sql
/*+ mycat:createDataSource{"name":"slave1","url":"jdbc:mysql://192.168.230.200:3306/mysql?useUnicode=true&serverTimezone=Asia/Shanghai&characterEncoding=UTF-8","user":"root","password":"root" } */;
```

添加从库2（只读）

```sql
/*+ mycat:createDataSource{"name":"slave2","url":"jdbc:mysql://192.168.230.202:3306/mysql?useUnicode=true&serverTimezone=Asia/Shanghai&characterEncoding=UTF-8","user":"root","password":"root" } */;
```

查询添加的数据源

```sql
/*+ mycat:showDataSources{} */;
```

### 11、mycat中添加集群

如果上面配置的集群名称是prototype，则跳过此步骤

```sql
/*! mycat:createCluster{"name":"pt","masters":["master"],"replicas":["slave1","slave2"]} */;
```

查看集群信息 

```sql
/*+ mycat:showClusters{} */;
```

### 12、查看（修改）集群信息

如果集群名称是prototype则修改prototype.cluster.json

```bash
vim /data/mycat/conf/clusters/prototype.cluster.json
```

查看集群配置

```bash
vim /data/mycat/conf/clusters/pt.cluster.json
```

内容如下

```json
{
        "clusterType":"MASTER_SLAVE",
        "heartbeat":{
                "heartbeatTimeout":1000,
                "maxRetryCount":3,
                "minSwitchTimeInterval":300,
                "showLog":false,
                "slaveThreshold":0.0
        },
        "masters":[
                "master"
        ],
        "maxCon":2000,
        "name":"pt",
        "readBalanceType":"BALANCE_ALL",
        "replicas":[
                "slave1",
                "slave2"
        ],
        "switchType":"SWITCH"
}
```

**负载均衡策略（readBalanceType）取值说明：**

```
BALANCE_ALL
默认值，获取集群中所有数据源

BALANCE_ALL_READ
获取集群中允许读的数据源

BALANCE_READ_WRITE
获取集群中允许读写的数据源，但允许读的数据源优先

BALANCE_NONE
获取集群中允许写的数据源，即在主节点中选择
```

**switchType取值说明：**

```
NOT_SWITCH
不进行主从切换

SWITCH
进行主从切换
```

### 13、设置mycat日志

修改配置文件

```bash
vim /data/mycat/conf/wrapper.conf
```

添加参数

```ini
wrapper.java.additional.10=-Dlogback.configurationFile=./conf/logback.xml
```

修改logback.xml，将level改成debug

```bash
vim /data/mycat/conf/logback.xml
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
        <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
                <encoder>
                        <pattern>
                                %d[%level]%c{100}.%M:%L%m%n
                        </pattern>
                </encoder>
        </appender>

        <root level="debug">
                <appender-ref ref="CONSOLE" />
        </root>
</configuration>

```

### 14、重启mycat

```bash
/data/mycat/bin/./mycat restart
```

### 15、查看日志输出

```bash
tail -f /data/mycat/logs/wrapper.log
```

### 
