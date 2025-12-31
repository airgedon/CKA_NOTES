## Trouble shooting

```
controlplane:~$ k get po
The connection to the server 172.30.1.2:6443 was refused - did you specify the right host or port?
```
```
crictl ps -a
```
```
cat /var/log/pods/kube-system_kube-apiserver-controlplane_a5dcea7f9cfb03d285afbbb948171e73/kube-apiserver/5.log 
```

---
