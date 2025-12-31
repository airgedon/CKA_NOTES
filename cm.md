```
kubectl create cm -h
kubectl create cm trauerweide --from-literal tree=trauerweide
```
---
k8s docs -> configmaps 그냥

```yaml
kind: Pod
metadata:
  name: pod1
spec:
  containers:
  - name: pod1
    image: nginx:alpine
    env:
    - name: TREE1
      valueFrom:
        configMapKeyRef:
          name: trauerweide
          key: tree
    volumeMounts:
    - name: birke
      mountPath: /etc/birke
      readOnly: true
  volumes:
  - name: birke
    configMap:
      name: birke
```
```
kubectl exec pod1 -- env | grep "TREE1=trauerweide"
kubectl exec pod1 -- cat /etc/birke/tree
kubectl exec pod1 -- cat /etc/birke/level
kubectl exec pod1 -- cat /etc/birke/department
```
