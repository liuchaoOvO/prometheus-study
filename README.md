# prometheus-study

prometheus 学习 资料记录


## 一、启动prometheus
*1.先创建映射目录* <br/>
mkdir -p /d/k8s/prometheus/opt/prometheus<br/>
*2.配置prometheus.yml* <br/>
vim prometheus.yml
```
global:
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

 `docker run  -d --name myprometheus --restart=always -p 9090:9090 -v /d/k8s/prometheus/opt/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus` <br/>
 
docker windows下挂载目录和文件 参考文档： https://www.yii666.com/article/646638.html <br/>
  `docker run -v d:\k8s\prometheus\opt\prometheus\prometheus.yml:/etc/prometheus/prometheus.yml -d --name myprometheus --restart=always -p 9090:9090 prom/prometheus` <br/>


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

![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/b6f864f5-3ba5-47e9-bded-5855a3b5c63b)


---
# docker 配置 node-exporter 采集node 指标信息
1. 拉取镜像
`docker pull prom/node-exporter`
2. 启动node-exporter
```
docker run -d --name mynode-exporter -p 9100:9100 \
prom/node-exporter 
```
![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/9453944b-b488-4577-beef-97aa768326ef)

3、编辑prometheus配置文件，用于后续启动prometheus指定配置文件


```
global:
  scrape_interval:     60s
  evaluation_interval: 60s

scrape_configs:
  - job_name: prometheus
    static_configs:
      - targets: ['192.168.1.101:9090']
        labels:
          instance: prometheus
  - job_name: node
    static_configs:
      - targets: ['192.168.1.101:9100']
        labels:
          instance: node
```
关闭当时启动的myprometheus pod <br/>
`docker stop myprometheus` <br/>
删除myprometheus container<br/>
`docker rm myprometheus` <br/>
重启myprometheus pod <br/>
docker windows下挂载目录和文件 参考文档： https://www.yii666.com/article/646638.html <br/>
  `docker run -v d:\k8s\prometheus\opt\prometheus\prometheus.yml:/etc/prometheus/prometheus.yml -d --name myprometheus --restart=always -p 9090:9090 prom/prometheus` <br/>
![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/09daf9f7-f8b7-49d9-a7c3-75fb213b3765)

![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/bd73af04-6d9e-4a45-af1d-ef9fb1c20f73)


---
# 配置Alertmanager

## 拉取Alertmanager最新镜像 docker 命令
`docker pull prom/alertmanager`<br/>
## 启动Alertmanager docker 命令
docker windows下挂载目录和文件 参考文档： https://www.yii666.com/article/646638.html <br/>
windows 系统下 利用原生终端 执行如下命令
```
docker run -v d:\k8s\prometheus\opt\alertmanager:/etc/alertmanager -d -p 9093:9093 --name myalertmanager --restart=always prom/alertmanager
```
<br/>



## /etc/alertmanager/alertmanager.yml alertmanager.yml 文件内容 如下

```
# 全局配置项
global:
  resolve_timeout: 5m #超时,默认5min
  #邮箱smtp服务
  smtp_smarthost: 'smtp.qq.com:465'
  smtp_from: '11111111@qq.com'
  smtp_auth_username: '11111111@qq.com'
  smtp_auth_password: '123456'
  smtp_require_tls: false

# 定义模板信息
templates:
  - 'template/*.tmpl'   # 路径

# 路由
route:
  group_by: ['alertname'] # 报警分组依据
  group_wait: 10s #组等待时间
  group_interval: 10s # 发送前等待时间
  repeat_interval: 1h #重复周期
  receiver: 'mail' # 默认警报接收者

# 警报接收者
receivers:
- name: 'mail' #警报名称
  email_configs:
  - to: '{{ template "email.to" . }}'  #接收警报的email
    html: '{{ template "email.to.html" . }}' # 模板
    send_resolved: true

# 告警抑制
inhibit_rules:
  - source_match:
      severity: 'critical'
    target_match:
      severity: 'warning'
    equal: ['alertname', 'dev', 'instance']
```
## 编写prometheus.yml配置文件 增加对alertmanager的配置
```
global:
  scrape_interval:     60s
  evaluation_interval: 60s
alerting:
  alertmanagers:
  - static_configs:
    - targets: ["192.168.1.101:9093"]
rule_files:
  - /etc/prometheus/rules.yml
  
scrape_configs:
  - job_name: prometheus
    static_configs:
      - targets: ['192.168.1.101:9090']
        labels:
          instance: prometheus
  - job_name: node
    static_configs:
      - targets: ['192.168.1.101:9100']
        labels:
          instance: node      
```
## 编写rules.yml配置文件   在d:\k8s\prometheus\opt\prometheus\rules.yml 编写

```
groups:
  - name: Warning
    rules:
      - alert: NodeMemoryUsage
        expr: 100 - (node_memory_MemFree_bytes + node_memory_Cached_bytes + node_memory_Buffers_bytes) / node_memory_MemTotal_bytes*100 > 80
        for: 1m
        labels:
          status: Warning
        annotations:
          summary: "{{$labels.instance}}: 内存使用率过高"
          description: "{{$labels.instance}}: 内存使用率大于 80% (当前值: {{ $value }}"

      - alert: NodeCpuUsage
        expr: (1-((sum(increase(node_cpu_seconds_total{mode="idle"}[1m])) by (instance)) / (sum(increase(node_cpu_seconds_total[1m])) by (instance)))) * 100 > 70
        for: 1m
        labels:
          status: Warning
        annotations:
          summary: "{{$labels.instance}}: CPU使用率过高"
          description: "{{$labels.instance}}: CPU使用率大于 70% (当前值: {{ $value }}"

      - alert: NodeDiskUsage
        expr: 100 - node_filesystem_free_bytes{fstype=~"xfs|ext4"} / node_filesystem_size_bytes{fstype=~"xfs|ext4"} * 100 > 80
        for: 1m
        labels:
          status: Warning
        annotations:
          summary: "{{$labels.instance}}: 分区使用率过高"
          description: "{{$labels.instance}}: 分区使用大于 80% (当前值: {{ $value }}"

      - alert: Node-UP
        expr: up{job='node-exporter'} == 0
        for: 1m
        labels:
          status: Warning
        annotations:
          summary: "{{$labels.instance}}: 服务宕机"
          description: "{{$labels.instance}}: 服务中断超过1分钟"

      - alert: TCP
        expr: node_netstat_Tcp_CurrEstab > 1000
        for: 1m
        labels:
          status: Warning
        annotations:
          summary: "{{$labels.instance}}: TCP连接过高"
          description: "{{$labels.instance}}: 连接大于1000 (当前值: {{$value}})"

      - alert: IO
        expr: 100 - (avg(irate(node_disk_io_time_seconds_total[1m])) by(instance)* 100) < 60
        for: 1m
        labels:
          status: Warning
        annotations:
          summary: "{{$labels.instance}}: 流入磁盘IO使用率过高"
          description: "{{$labels.instance}}:流入磁盘IO大于60%  (当前值:{{$value}})"
```

**注意：映射挂载的路径要跟prometheus.yml中的rule_files的配置的路径一样** <br/>
*docker windows下挂载目录和文件 参考文档： https://www.yii666.com/article/646638.html <br/>*
  `docker run -v d:\k8s\prometheus\opt\prometheus\prometheus.yml:/etc/prometheus/prometheus.yml -v d:\k8s\prometheus\opt\prometheus\rules.yml:/etc/prometheus/rules.yml -d --name myprometheus --restart=always -p 9090:9090 prom/prometheus` <br/>
关闭当时启动的myprometheus pod <br/>
`docker stop myprometheus` <br/>
删除myprometheus container<br/>
`docker rm myprometheus` <br/>
![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/a61bf556-e755-4af6-8a3f-d80e511c99d5)
重启myprometheus pod <br/>
![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/3fb0dc78-df02-4932-8313-09eb6fd087f2)

*docker ps -a 查看启动后的情况*
![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/a39052ab-3fef-4690-9593-fdd1145b213b)
* 访问ip:9093 如 http://192.168.1.101:9093/#/alerts
* ![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/2545c404-fa9a-4757-9f8e-a023a3ecffb6)
* 访问ip:9093 如 http://192.168.1.101:9093/#/status
* ![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/f493c4e1-2462-4762-8aec-79b92c7fd529)
* 访问ip:9090 如  http://127.0.0.1:9090/alerts
* ![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/a1d9eae3-fa6b-47d6-9eaf-9f1f90247ea5)
* 访问ip:9090 如  http://127.0.0.1:9090/rules
* ![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/ff11907a-66ab-4af2-a802-022d1a21246c)

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

访问：http://127.0.0.1:3000/ 的采集到的node exporter 信息在 grafana 展示的效果如下图：
![image](https://github.com/liuchaoOvO/prometheus-study/assets/34876517/2e94098d-2460-4cdd-98a8-1b733b0d04c5)

