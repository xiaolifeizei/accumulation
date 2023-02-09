### 一、Docker安装

```
docker run -dt -p 9000:9000 -p 9090:9090 --name minio -v D:\minio\data:/data -e "MINIO_ROOT_USER=ROOTUSER" -e "MINIO_ROOT_PASSWORD=CHANGEME123" quay.io/minio/minio server /data --console-address ":9090" --address ":9000"
```

