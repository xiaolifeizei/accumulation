### 运行dubbo-admin

```
docker pull apache/dubbo-admin

docker run -d --name dubbo-admin -p 8080:8080 -e admin.registry.address=zookeeper://192.168.31.23:2181 apache/dubbo-admin
```