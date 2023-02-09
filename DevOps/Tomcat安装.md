### 一、Linux环境

1、解压缩

```bash
tar -zxvf apache-tomcat-7.0.99.tar.gz 
```

2、配置环境变量

```bash
vim /etc/profile
```

3、添加如下配置

```ini
export CATALINA_BASE=/root/tomcat/apache-tomcat-7.0.99
export CATALINA_HOME=/root/tomcat/apache-tomcat-7.0.99
export TOMCAT_HOME=/root/tomcat/apache-tomcat-7.0.99
```

4、运行

```bash
./startup.sh
```

