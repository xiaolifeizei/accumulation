### CenterOS 安装docker

```
# 卸载旧版本
yum remove docker  docker-common docker-selinux docker-engine

# 更新软件源
yum -y update

# 安装需要的软件包
yum install -y yum-utils device-mapper-persistent-data lvm2

# 设置yum源
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 查询版本号
yum list docker-ce --showduplicates | sort -r

# 安装指定版本
yum -y install docker-ce-20.10.9-3.el7

# 启动并设置开机自动启动
systemctl start docker & systemctl enable docker
```

### docker镜像加速

```
# 修改如下文件
vim /etc/docker/daemon.json

# 添加镜像地址
{
    "registry-mirrors" : [
      "https://registry.docker-cn.com",
      "https://docker.mirrors.ustc.edu.cn",
      "http://hub-mirror.c.163.com",
      "https://cr.console.aliyun.com/"
  ]
}

# 重启docker
systemctl daemon-reload & systemctl restart docker
```

### 安装docker-compose

```
# 下载安装
curl -L "https://get.daocloud.io/docker/compose/releases/download/v2.6.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# 添加执行权限
chmod +x /usr/local/bin/docker-compose
```

### 运行Redis

```
docker pull redis
docker run -d --name redis -p 6379:6379 redis
```

### 运行MySQL

```
docker pull mysql
docker run -d --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=mypassword mysql
```

### 运行nacos

```
docker pull nacos/nacos-server

docker run --name nacos -e MODE=standalone -e SPRING_DATASOURCE_PLATFORM=mysql -e MYSQL_SERVICE_HOST=192.168.31.23 -e MYSQL_SERVICE_DB_NAME=nacos -e MYSQL_SERVICE_USER=root -e MYSQL_SERVICE_PASSWORD=root -p 8848:8848 -p 9848:9848 -p 9849:9849 -d nacos/nacos-server
```

### 运行Nginx

```
docker pull nginx
docker run --name nginx
# 拷贝配置目录和html目录
docker cp nginx:/etc/nginx /root/docker/
docker cp nginx:/usr/share/nginx/html /root/docker/
docker stop nginx
docker rm nginx
docker run -d --name nginx -p 80:80 -p 443:443 -v /root/docker/nginx/:/etc/nginx/ -v /root/docker/html/:/usr/share/nginx/html nginx
```

### 运行dubbo-admin

```
docker pull apache/dubbo-admin
docker run -d --name dubbo-admin -p 8080:8080 -e admin.registry.address=zookeeper://192.168.31.23:2181 apache/dubbo-admin
```


### windows10 报 error during connect 错误

```
cd "C:\Program Files\Docker\Docker"

./DockerCli.exe -SwitchDaemon
```

### Docker中安装ping、ifconfig、netstat等命令

```bash
apt-get update
# 安装net工具
apt install net-tools
# 安装ping命令
apt install iputil-ping
```

### 进入docker容器

```bash
docker exec -it 容器名 /bin/bash
```

### 打印docker运行日志

```bash
docker logs --tail=500 容器名
```

