必须在创建local-path类型的pvc之前修改，否则之前创建的pvc不受影响
```yaml
vi /var/lib/rancher/k3s/server/manifests/local-storage.yaml
```
![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e4a658f7-5596-40a5-8554-1d11a53791ef/Untitled.png)
```yaml
kubectl apply -f /var/lib/rancher/k3s/server/manifests/local-storage.yaml
```
