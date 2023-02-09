### Linux下配置

1、解压缩

```bash
tar -zxvf jdk-8u202-linux-x64.tar.gz
```

2、配置环境变量

```bash
vim /etc/profile
```

3、在文件最后，配置环境变量如下

```ini
export JAVA_HOME=/root/tomcat/jdk1.8.0_202
export JRE_HOME=/root/tomcat/jdk1.8.0_202/jre
export CLASS_PATH=.:$JAVA_HOME/lib:$JRE_HOME/lib
export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
```

4、让配置生效

```bash
source /etc/profile
```