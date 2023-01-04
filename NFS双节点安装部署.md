## Intallation

### NFS server
安装并启动服务
```bash
sudo apt install nfs-kernel-server
sudo systemctl start nfs-kernel-server
```
创建共享存储文件夹，并给予权限
```bash
sudo mkdir /opt/share
sudo chmod -R 777 /opt/share
```
NFS服务的配置文件为 /etc/exports，这个文件是NFS的主要配置文件，不过系统并没有默认值，
所以这个文件不一定会存在，可能要使用vim手动建立，然后在文件里面写入配置内容。
```bash
sudo vim /etc/exports
```

for example:

```bash
/opt/share *(rw,sync,no_root_squash,no_subtree_check,no_all_squash,insecure)
```
rw：设置输出目录读写。
sync：将数据同步写入内存缓冲区与磁盘中，效率低，但可以保证数据的一致性。
no_root_squash：不将root用户及所属组都映射为匿名用户或用户组。
no_subtree_check：即使输出目录是一个子目录，nfs服务器也不检查其父目录的权限，这样可以提高效率。
no_all_squash：不将远程访问的所有普通用户及所属组都映射为匿名用户或用户组
insecure：允许客户端从大于1024的tcp/ip端口连接服务器。

使修改生效
```bash
sudo exportfs -a
```

### NFS client
安装nfs client
```bash
sudo apt install nfs-common
```
创建共享存储文件夹，并给予权限
```bash
sudo mkdir /opt/share
sudo chmod -R 777 /opt/share
```
挂载共享目录到nfs server服务器的ip下
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

## K3s使用nfs作为PVC储存卷
创建rbac.yaml

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: default
---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: nfs-client-provisioner-runner
rules:
  - apiGroups: [""]
    resources: ["persistentvolumes"]
    verbs: ["get", "list", "watch", "create", "delete"]
  - apiGroups: [""]
    resources: ["persistentvolumeclaims"]
    verbs: ["get", "list", "watch", "update"]
  - apiGroups: ["storage.k8s.io"]
    resources: ["storageclasses"]
    verbs: ["get", "list", "watch"]
  - apiGroups: [""]
    resources: ["events"]
    verbs: ["create", "update", "patch"]
---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: run-nfs-client-provisioner
subjects:
  - kind: ServiceAccount
    name: nfs-client-provisioner
    # replace with namespace where provisioner is deployed
    namespace: default
roleRef:
  kind: ClusterRole
  name: nfs-client-provisioner-runner
  apiGroup: rbac.authorization.k8s.io
---
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: leader-locking-nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: default
rules:
  - apiGroups: [""]
    resources: ["endpoints"]
    verbs: ["get", "list", "watch", "create", "update", "patch"]
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: leader-locking-nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: default
subjects:
  - kind: ServiceAccount
    name: nfs-client-provisioner
    # replace with namespace where provisioner is deployed
    namespace: default
roleRef:
  kind: Role
  name: leader-locking-nfs-client-provisioner
  apiGroup: rbac.authorization.k8s.io
```
```kubectl apply -f rbac.yaml```
  
创建storageclass(class.yaml)
```apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-nfs-storage
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"
provisioner: k8s-sigs.io/nfs-subdir-external-provisioner # or choose another name, must match deployment's env PROVISIONER_NAME'
parameters:
  archiveOnDelete: "false"
```
```kubectl apply -f class.yaml```

创建deployment.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nfs-client-provisioner
  labels:
    app: nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: default
spec:
  replicas: 1
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: nfs-client-provisioner
  template:
    metadata:
      labels:
        app: nfs-client-provisioner
    spec:
      serviceAccountName: nfs-client-provisioner
      containers:
        - name: nfs-client-provisioner
          image: registry.cn-hangzhou.aliyuncs.com/lzf-k8s/k8s-nfs-storage:1.0.0
          volumeMounts:
            - name: nfs-client-root
              mountPath: /persistentvolumes
          env:
            - name: PROVISIONER_NAME
              value: k8s-sigs.io/nfs-subdir-external-provisioner
            - name: NFS_SERVER
              value: 172.16.30.247
            - name: NFS_PATH
              value: /opt/share
      volumes:
        - name: nfs-client-root
          nfs:
            server: 172.16.30.247
            path: /opt/share
```
```kubectl apply -f deployment.yaml```

创建PVC(test-claim.yaml)
```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: test-claim
spec:
  storageClassName: managed-nfs-storage
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 500Mi
``` 
```kubectl apply -f test-claim.yaml```












