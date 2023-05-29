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

---
# grafana 配置 prometheus 数据源

![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/0946048e-e28c-42fa-b502-1762f6f2f512)
![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/4e1bda2a-00dd-4f7f-8f9a-ccc9f4fa7843)

ipconfig 查看ip 地址。 因为是docker 容器部署 ip不能直接用127.0.0.1 参考：https://www.cnblogs.com/youxin/p/16212618.html
![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/f27b26fd-47b5-41ed-977c-a2f8e375664c)
![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/b6f864f5-3ba5-47e9-bded-5855a3b5c63b)

---
# 导入dashboard展示

1、grafana官网dashboards地址：
https://grafana.com/grafana/dashboards

下载了一个 node_exporter 的图形模板：
 
https://grafana.com/grafana/dashboards/8919
 
点击Load，就可以下载Node Exporter的dashboard。
选择Folder，选择Prometheus数据源，Import。

![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/58bac769-1a55-4bdc-9e43-dedf0f3b4459)


![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/4815b0d4-a9f4-4dcc-ae12-b60c1b68a84a)
后续 可以配置导入 node_exporter 采集的指标数据和 dashboard 规则信息。展示出来
