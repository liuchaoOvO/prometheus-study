# prometheus-study
prometheus 学习 资料记录



一、启动prometheus
1.先创建映射目录
mkdir -p /d/k8s/prometheus/opt/prometheus
2.配置prometheus.yml
vim prometheus.yml
global:
  scrape_interval:     20s
  evaluation_interval: 20s

scrape_configs:
  - job_name: prometheus
    static_configs:
      - targets: ['localhost:9090']
        labels:
          instance: prometheus
二、启动prometheus docker 命令

 docker run  -d --name myprometheus --restart=always -p 9090:9090 -v /d/k8s/prometheus/opt/prometheus/prometheus.yml prom/prometheus
