给需要调度的节点增加label

```bash
kubectl label nodes <node_name> <label>
```

```bash
kubectl get nodes --show-labels
```

举例：单mongo实例

其中**key values对应label**（加在spec/template/spec下）
```yaml
#template:
    #metadata:
      #labels:
        #app: mongo
    spec:
      affinity:
        nodeAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - preference:
              matchExpressions:
              - key: perfer
                operator: In
                values:
                - mongo
            weight: 1
```
