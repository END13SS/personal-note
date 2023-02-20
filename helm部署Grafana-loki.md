# 离线安装Helm
https://github.com/helm/helm/releases

```cd /opt/helm
tar -zxvf helm-v3.10.2-linux-amd64.tar.gz
mv linux-amd64/helm /usr/local/bin/helm```
查看helm版本
```helm version```
Helm安装chart需要kube config，直接复制k3s.yaml生成kube config
```cd /root #k3s安装时用户主目录
sudo cp /etc/rancher/k3s/k3s.yaml .kube/config
sudo chmod +rw .kube/config```
安装Loki、Grafana、promtail
创建namespace
```kubectl create ns loki```
helm添加repo仓库
```helm repo add grafana https://grafana.github.io/helm-charts```
更新repo仓库
```helm repo update```
helm安装loki、grafana和promtail
```helm upgrade --install loki grafana/loki-stack --namespace=loki --set grafana.enabled=true```
等待安装
[图片]
若出现以下报错则需要重复运行几次
[图片]
安装成功
[图片]
```kubectl -n loki get all```
[图片]
可以看到pod正在创建中，稍等片刻
在等待的过程中可以修改下loki-grafana的端口暴露方式
```kubectl -n loki edit svc loki-grafana```
[图片]
如上图，增加红色框中的内容，修改黄色框中的内容，保存并退出
```kubectl -n loki get svc```
确认修改已生效
[图片]
确保pod已running
[图片]
访问：http://服务器IP:3000 进入grafana登录界面
[图片]
用户名为admin，密码通过以下命令获取
```kubectl get secret --namespace loki loki-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo```
[图片]
成功登录
[图片]
左侧导航栏选择Explore进入
[图片]
单击Log browser出现下拉框选项，本文档演示了loki namespace中的所有container
[图片]
点击Show logs，如下图显示日志信息
[图片]
