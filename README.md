# prometheus-study

prometheus 学习 资料记录



## 一、启动prometheus
*1.先创建映射目录* <br/>
mkdir -p /d/k8s/prometheus/opt/prometheus<br/>
*2.配置prometheus.yml* <br/>
vim prometheus.yml
```global:
  scrape_interval:     20s
  evaluation_interval: 20s

scrape_configs:
  - job_name: prometheus
    static_configs:
      - targets: ['localhost:9090']
        labels:
          instance: prometheus
```
## 二、启动prometheus docker 命令

 `docker run  -d --name myprometheus --restart=always -p 9090:9090 -v /d/k8s/prometheus/opt/prometheus/prometheus.yml prom/prometheus`


样式：
![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/2654cc5f-f25e-4837-b34e-2879f99d19fb)
![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/889d18be-9088-45c9-96cc-410029f390c4)

访问：http://127.0.0.1:9090/graph
如下图 证明启动成功
![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/577d0fdb-d964-4079-929a-f5187c3624c8)
访问：http://127.0.0.1:9090/targets
查看当前采集的target信息
![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/529e0262-ae7f-4f01-a3e5-2d0c7beb6aa0)

---
# 创建grafana

1. mkdir -p /d/k8s/grafana/opt/grafana-storage
2. 添加权限
`chmod 777 -R /d/k8s/grafana/opt/grafana-storage`
因为grafana用户会在这个目录写入文件，直接设置777，比较简单粗暴！
启动grafana 命令

`docker run -d --name mygrafana --restart=always -p 3000:3000  -v /d/k8s/grafana/opt/grafana-storage:/var/lib/grafana grafana/grafana`
![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/f87acc9b-4c71-4f96-bc9b-9bf6cc2223cf)


访问url：    127.0.0.1:3000/

默认会先跳转到登录页面，默认的用户名和密码都是admin
登录之后，它会要求你重置密码。你还可以再输次admin密码！
密码设置完成之后，就会跳转到首页
![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/7c493d40-cdff-4303-9818-53d1abab864a)

![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/1b5005de-3238-4082-be53-6e4ba5a8542a)


## 利用 docker ps 查看启动情况
![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/56657f28-d7fb-430e-ad21-a9b2327ac9db)

