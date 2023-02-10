# Prometheus安装配置

## 资源

- github地址：https://github.com/prometheus-community
- exporter下载：https://prometheus.io/docs/instrumenting/exporters/

## 一、Docker安装

### 1、下载镜像

```bash
docker pull prom/prometheus
```

### 2、创建配置文件

```bash
prometheus.yml
```

配置文件如下：

```yaml
global:
  # 每15s采集一次数据
  scrape_interval: 60s
  # 每15s做一次告警检测
  evaluation_interval: 60s
# 采集配置，配置数据源，包含分组job_name以及具体target，又分为静态配置和服务发现  
scrape_configs:
  # 任务目标名，可以理解成分组，每个分组包含具体的target组员
  - job_name: prometheus
    # 这里如果单独设定的话，会覆盖global设定的参数，拉取时间间隔为10s
    scrape_interval: 60s
    static_configs:
      - targets: ['127.0.0.1:9090']
        labels:
          instance: prometheus

  - job_name: windows
    static_configs:
      - targets: ['192.168.37.1:9182']

  - job_name: mysql
    static_configs:
      - targets: ['192.168.37.1:9104']
```

### 3、运行容器

```bash
docker run -itd --name prometheus -p 9090:9090 -v /prometheus/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
```

## 二、Linux安装

