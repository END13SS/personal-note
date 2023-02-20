### 离线安装Helm
https://github.com/helm/helm/releases

```bash
cd /opt/helm
tar -zxvf helm-v3.10.2-linux-amd64.tar.gz
mv linux-amd64/helm /usr/local/bin/helm```

查看helm版本

```bash
helm version```

Helm安装chart需要kube config，直接复制k3s.yaml生成kube config

```cd /root #k3s安装时用户主目录
sudo cp /etc/rancher/k3s/k3s.yaml .kube/config
sudo chmod +rw .kube/config```

### 安装Loki、Grafana、promtail
创建namespace

```kubectl create ns loki```

helm添加repo仓库

```helm repo add grafana https://grafana.github.io/helm-charts```

更新repo仓库

```helm repo update```

helm安装loki、grafana和promtail

```helm upgrade --install loki grafana/loki-stack --namespace=loki --set grafana.enabled=true```

等待安装
![image](https://user-images.githubusercontent.com/89510761/220135263-622fda85-b5de-4ddc-93de-0cff9e0334e9.png)

若出现以下报错则需要重复运行几次
![image](https://user-images.githubusercontent.com/89510761/220135301-a7313397-2ca3-418e-86d5-fc75c124b105.png)

安装成功
![image](https://user-images.githubusercontent.com/89510761/220135343-57f816bb-ef48-4c88-a7fc-a7f9f86b55f3.png)

```kubectl -n loki get all```
![image](https://user-images.githubusercontent.com/89510761/220135363-f8765471-5444-4a9f-9b75-311064daebb9.png)

可以看到pod正在创建中，稍等片刻
在等待的过程中可以修改下loki-grafana的端口暴露方式

```kubectl -n loki edit svc loki-grafana```
![image](https://user-images.githubusercontent.com/89510761/220135407-61cdc8af-c0b4-4244-a000-d43e8af2db00.png)

如上图，增加红色框中的内容，修改黄色框中的内容，保存并退出

```kubectl -n loki get svc```

确认修改已生效
![image](https://user-images.githubusercontent.com/89510761/220135575-840294cb-14de-482f-96f0-e3f1d8134c77.png)

确保pod已running
![image](https://user-images.githubusercontent.com/89510761/220135611-e4895a8b-23ee-439e-bf1c-234db45e4100.png)
访问：http://服务器IP:3000 进入grafana登录界面
![image](https://user-images.githubusercontent.com/89510761/220135716-5e1fc68b-bcff-4eb7-bb0b-8259f476931d.png)
用户名为admin，密码通过以下命令获取
```kubectl get secret --namespace loki loki-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo```
![image](https://user-images.githubusercontent.com/89510761/220135734-7af715c6-80d0-45e4-aeb8-eb01a83134fa.png)

成功登录
![image](https://user-images.githubusercontent.com/89510761/220135756-77e5c2c5-5563-4fbe-8560-7e51a9131e68.png)

左侧导航栏选择Explore进入
![image](https://user-images.githubusercontent.com/89510761/220135776-4e3dcd19-bad9-4b03-8cf5-5e4703993c8a.png)

单击Log browser出现下拉框选项，本文档演示了loki namespace中的所有container
![image](https://user-images.githubusercontent.com/89510761/220135785-4e9112e2-363e-46b1-8b5e-09f1166e1efa.png)

点击Show logs，如下图显示日志信息
![image](https://user-images.githubusercontent.com/89510761/220135803-0dd62bdb-752b-483e-9a67-840fb5c1e46c.png)

