```
wget https://github.com/taosdata/TDengine-Operator/raw/3.0/helm/tdengine-3.0.2.tgz
```
K3s-1.25集群，使用namespace=middleware，storageClass=local-path(default)
```
helm install tdengine tdengine-3.0.2.tgz \
  --set storage.className=local-path \
  --set storage.dataSize=5Gi \
  --set storage.logSize=1Gi --namespace=middleware
  ```
helm部署的默认配置是单节点，手动扩容至3节点（按需调整）
```
kubectl -n middleware scale sts tdengine --replicas=3 
```
![image](https://user-images.githubusercontent.com/89510761/227771041-b5ce88c8-577d-4852-86ea-c94619fc2e3c.png)

其中dnode 为自动配置，可以使用 show dnodes 命令查看当前集群的节点：
```
kubectl -n middleware exec -i -t tdengine-0 -- taos -s "show dnodes"
kubectl -n middleware exec -i -t tdengine-1 -- taos -s "show dnodes"
kubectl -n middleware exec -i -t tdengine-2 -- taos -s "show dnodes"
```
![image](https://user-images.githubusercontent.com/89510761/227771064-52f0c54d-f661-4fd5-81d5-b98af0958c3b.png)

helm部署的默认配置服务暴露方式是ClusterIP，按需修改为NodePort对外暴露端口:
此演示中只对外暴露两个重要端口（6030、6041）
手动删除原本的service
```
kubectl -n middleware delete svc tdengine
```
应用下方的yaml：
```
kubectl apply -f svc-tdengine.yaml
```
```
apiVersion: v1
items:
- apiVersion: v1
  kind: Service
  metadata:
    annotations:
      meta.helm.sh/release-name: tdengine
      meta.helm.sh/release-namespace: middleware
    labels:
      app.kubernetes.io/instance: tdengine
      app.kubernetes.io/managed-by: Helm
      app.kubernetes.io/name: tdengine
      app.kubernetes.io/version: 3.0.2.2
      helm.sh/chart: tdengine-3.0.2
    name: tdengine
    namespace: middleware
  spec:
    ports:
    - name: tcp0
      port: 6030
      protocol: TCP
      targetPort: 6030
      nodePort: 6030
    - name: tcp1
      port: 6041
      protocol: TCP
      targetPort: 6041
      nodePort: 6041
    selector:
      app: taosd
      app.kubernetes.io/instance: tdengine
      app.kubernetes.io/name: tdengine
    sessionAffinity: None
    type: NodePort
  status:
    loadBalancer: {}
kind: List
metadata:
  resourceVersion: ""
```
![image](https://user-images.githubusercontent.com/89510761/227771122-1ea57593-b0b9-4d7c-a4bb-14819bc2281a.png)

若需要使用外部工具连接数据库，参考[GitHub - arielyang/TDengineGUI: A simple TDengine DeskTop Manager](https://github.com/arielyang/TDengineGUI)
