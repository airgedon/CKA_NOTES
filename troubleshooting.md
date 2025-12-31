## Trouble shooting
```
cp /etc/kubernetes/manifests/kube-apiserver.yaml ~/kube-apiserver.yaml.ori
```
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


1) if we would check the /var directory
```
cat /var/log/pods/kube-system_kube-apiserver-controlplane_e24b3821e9bdc47a91209bfb04056993/kube-apiserver/X.log

> Err: connection error: desc = "transport: Error while dialing dial tcp: address this-is-very-wrong: missing port in address". Reconnecting...
```
2) but here we want to find other ways, so we check the container logs
```
crictl ps # maybe run a few times, because the apiserver container get's restarted
crictl logs f669a6f3afda2
> Error while dialing dial tcp: address this-is-very-wrong: missing port in address. Reconnecting...
```
3) what about syslogs
```
journalctl | grep apiserver # nothing specific
cat /var/log/syslog | grep apiserver # nothing specific
tail -f /var/log/syslog | grep apiserver
```

```
cp ~/kube-apiserver.yaml.ori /etc/kubernetes/manifests/kube-apiserver.yaml
```
---

```
k get no
ssh node01
service kubelet status
cat /var/log/syslog | grep kubelet
find / | grep kubeadm
```
```
--kubeconfig=/etc/kubernetes/kubelet.conf
```
To fix we remove the unknown flag in:

```
/var/lib/kubelet/kubeadm-flags.env
service kubelet restart
service kubelet status
```
```
kubectl -n management logs -h

kubectl -n management logs deploy/collect-data -c nginx >> /root/logs.log

kubectl -n management logs deploy/collect-data -c httpd >> /root/logs.log

kubectl -n management logs --all-containers deploy/collect-data > /root/logs.log
```
