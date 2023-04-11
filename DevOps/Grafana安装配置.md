## 一、Docker安装

### 1、docker安装

```
docker run -itd --name grafana -p 3000:3000 grafana/grafana-enterprise 
```

### 2、登录

访问http://127.0.0.1:3000打开登录页面

用户名：admin

密码：admin

### 3、监控模板

下载地址：https://grafana.com/grafana/dashboards

1、mysql

https://grafana.com/grafana/dashboards/7362-mysql-overview/

2、windows

https://grafana.com/grafana/dashboards/14694-windows-exporter-dashboard/

3、Druid

https://grafana.com/grafana/dashboards/11157-druid-connection-pool-dashboard/
