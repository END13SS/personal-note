## Intallation

### NFS server

```bash
sudo apt install nfs-kernel-server
sudo systemctl start nfs-kernel-server
```

```bash
sudo mkdir /opt/share
sudo chmod -R 777 /opt/share
```

```bash
	sudo vim /etc/exports
```

for example:

```bash
/opt/share *(rw,sync,no_root_squash,no_subtree_check)
```

```bash
sudo exportfs -a
```

### NFS client

```bash
sudo apt install nfs-common
```

```bash
sudo mkdir /opt/share
sudo chmod -R 777 /opt/share
```

```bash
sudo mount 172.16.30.240:/opt/share /opt/share
```

设置client开机挂载

```bash
sudo vim /etc/fstab
```

添加内容

```bash
172.16.30.240:/opt/share /opt/share nfs rw 0 0
```

[史上最全之K8s使用nfs作为存储卷的五种方式](http://www.lishuai.fun/2021/08/12/k8s-nfs-pv/#/%E5%88%9B%E5%BB%BA%E7%B1%BB%E5%9E%8B%E4%B8%BAnfs%E7%9A%84%E6%8C%81%E4%B9%85%E5%8C%96%E5%AD%98%E5%82%A8%E5%8D%B7)

## K8s使用nfs作为储存卷

创建pv

```bash
sudo vim pv-nfs.yaml
```

yaml内容：

```bash
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  storageClassName: manual
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  nfs:
    path: /opt/share
    server: 172.16.30.240
```

创建pvc

```bash
sudo vim pvc-nfs.yaml
```

yaml内容：

```bash
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-nfs
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
```

创建busybox容器验证

```bash
sudo vim busy.yaml
```

yaml内容：

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: busybox
  labels:
    app: busybox
spec:
  replicas: 1
  selector:
    matchLabels:
      app: busybox
  template:
    metadata:
      labels:
        app: busybox
    spec:
      containers:
      - name: busybox
        image: busybox
        command: ['sh', '-c', 'echo "Hello, Kubernetes!" && sleep 3600']
        volumeMounts:
        - name: data
          mountPath: /data
      volumes:
      - name: data
        persistentVolumeClaim:
          claimName: pvc-nfs
```

查看pod

```bash
kubectl get pods
```

```bash
NAME                      READY   STATUS    RESTARTS   AGE
busybox-8fdfb7d89-mkmns   1/1     Running   0          4m16s
```

进入容器
```## Intallation

### NFS server

```bash
sudo apt install nfs-kernel-server
sudo systemctl start nfs-kernel-server
```

```bash
sudo mkdir /opt/share
sudo chmod -R 777 /opt/share
```

```bash
	sudo vim /etc/exports
```

for example:

```bash
/opt/share *(rw,sync,no_root_squash,no_subtree_check)
```

```bash
sudo exportfs -a
```

### NFS client

```bash
sudo apt install nfs-common
```

```bash
sudo mkdir /opt/share
sudo chmod -R 777 /opt/share
```

```bash
sudo mount 172.16.30.240:/opt/share /opt/share
```

设置client开机挂载

```bash
sudo vim /etc/fstab
```

添加内容

```bash
172.16.30.240:/opt/share /opt/share nfs rw 0 0
```

[史上最全之K8s使用nfs作为存储卷的五种方式](http://www.lishuai.fun/2021/08/12/k8s-nfs-pv/#/%E5%88%9B%E5%BB%BA%E7%B1%BB%E5%9E%8B%E4%B8%BAnfs%E7%9A%84%E6%8C%81%E4%B9%85%E5%8C%96%E5%AD%98%E5%82%A8%E5%8D%B7)

## K8s使用nfs作为储存卷

创建pv

```bash
sudo vim pv-nfs.yaml
```

yaml内容：

```bash
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  storageClassName: manual
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  nfs:
    path: /opt/share
    server: 172.16.30.240
```

创建pvc

```bash
sudo vim pvc-nfs.yaml
```

yaml内容：

```bash
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-nfs
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
```

创建busybox容器验证

```bash
sudo vim busy.yaml
```

yaml内容：

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: busybox
  labels:
    app: busybox
spec:
  replicas: 1
  selector:
    matchLabels:
      app: busybox
  template:
    metadata:
      labels:
        app: busybox
    spec:
      containers:
      - name: busybox
        image: busybox
        command: ['sh', '-c', 'echo "Hello, Kubernetes!" && sleep 3600']
        volumeMounts:
        - name: data
          mountPath: /data
      volumes:
      - name: data
        persistentVolumeClaim:
          claimName: pvc-nfs
```

查看pod

```bash
kubectl get pods
```

```bash
NAME                      READY   STATUS    RESTARTS   AGE
busybox-8fdfb7d89-mkmns   1/1     Running   0          4m16s
```
进入容器
```## Intallation

### NFS server

```bash
sudo apt install nfs-kernel-server
sudo systemctl start nfs-kernel-server
```

```bash
sudo mkdir /opt/share
sudo chmod -R 777 /opt/share
```

```bash
	sudo vim /etc/exports
```

for example:

```bash
/opt/share *(rw,sync,no_root_squash,no_subtree_check)
```

```bash
sudo exportfs -a
```

### NFS client

```bash
sudo apt install nfs-common
```

```bash
sudo mkdir /opt/share
sudo chmod -R 777 /opt/share
```

```bash
sudo mount 172.16.30.240:/opt/share /opt/share
```

设置client开机挂载

```bash
sudo vim /etc/fstab
```

添加内容

```bash
172.16.30.240:/opt/share /opt/share nfs rw 0 0
```

[史上最全之K8s使用nfs作为存储卷的五种方式](http://www.lishuai.fun/2021/08/12/k8s-nfs-pv/#/%E5%88%9B%E5%BB%BA%E7%B1%BB%E5%9E%8B%E4%B8%BAnfs%E7%9A%84%E6%8C%81%E4%B9%85%E5%8C%96%E5%AD%98%E5%82%A8%E5%8D%B7)

## K8s使用nfs作为储存卷

创建pv

```bash
sudo vim pv-nfs.yaml
```

yaml内容：

```bash
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  storageClassName: manual
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  nfs:
    path: /opt/share
    server: 172.16.30.240
```

创建pvc

```bash
sudo vim pvc-nfs.yaml
```

yaml内容：

```bash
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-nfs
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
```

创建busybox容器验证

```bash
sudo vim busy.yaml
```

yaml内容：

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: busybox
  labels:
    app: busybox
spec:
  replicas: 1
  selector:
    matchLabels:
      app: busybox
  template:
    metadata:
      labels:
        app: busybox
    spec:
      containers:
      - name: busybox
        image: busybox
        command: ['sh', '-c', 'echo "Hello, Kubernetes!" && sleep 3600']
        volumeMounts:
        - name: data
          mountPath: /data
      volumes:
      - name: data
        persistentVolumeClaim:
          claimName: pvc-nfs
```

查看pod

```bash
kubectl get pods
```

```bash
NAME                      READY   STATUS    RESTARTS   AGE
busybox-8fdfb7d89-mkmns   1/1     Running   0          4m16s
```

进入容器
```kubectl exec -it busybox-8fdfb7d89-mkmns -- sh
cd /data
ls```





