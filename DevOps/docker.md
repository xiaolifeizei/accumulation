### 运行dubbo-admin

```
docker pull apache/dubbo-admin

docker run -d --name dubbo-admin -p 8080:8080 -e admin.registry.address=zookeeper://192.168.31.23:2181 apache/dubbo-admin
```

### 运行nacos

```
docker pull nacos/nacos-server

docker run --name nacos -e MODE=standalone -e SPRING_DATASOURCE_PLATFORM=mysql -e MYSQL_SERVICE_HOST=192.168.31.23 -e MYSQL_SERVICE_DB_NAME=nacos -e MYSQL_SERVICE_USER=root -e MYSQL_SERVICE_PASSWORD=root -p 8848:8848 -p 9848:9848 -p 9849:9849 -d nacos/nacos-server
```
