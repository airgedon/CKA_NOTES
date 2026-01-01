Note:

It is not possible to directly specify the name of the namespaces in a NetworkPolicy. You must use a namespaceSelector with matchLabels or matchExpressions to select the namespaces based on their labels.

Below is the complete network policy configuration:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-policy
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          name: api-pod
    ports:
    - protocol: TCP
      port: 3306
```

Note

In this configuration:

The podSelector targets the database pod through its label.
The policyTypes field specifies that only ingress traffic is affected.
The ingress rule allows traffic specifically from pods with the label name: api-pod on TCP port 3306.
Keep in mind that isolation only applies to the traffic explicitly defined under policyTypes. Unspecified traffic is automatically allowed by default.


```
k get ns --show-labels
```
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: np
  namespace: space1
spec:
  podSelector: {}
  policyTypes:
  - Egress
  egress:
  - to:
     - namespaceSelector:
        matchLabels:
         kubernetes.io/metadata.name: space2
  - ports:
    - port: 53
      protocol: TCP
    - port: 53
      protocol: UDP
```
---

```
kubectl get pod -A --show-labels

# there are tester pods that you can use
kubectl get svc,pod -A --show-labels | grep tester
```

```
kubectl exec tester-0 -- curl tester.level-1000.svc.cluster.local
kubectl exec tester-0 -- curl tester.level-1001.svc.cluster.local
kubectl exec tester-0 -- curl tester.level-1002.svc.cluster.local
```
---

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: internal-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      name: internal
  policyTypes:
  - Egress
  - Ingress
  ingress:
    - {}
  egress:
  - to:
    - podSelector:
        matchLabels:
          name: mysql
    ports:
    - protocol: TCP
      port: 3306

  - to:
    - podSelector:
        matchLabels:
          name: payroll
    ports:
    - protocol: TCP
      port: 8080

  - ports:
    - port: 53
      protocol: UDP
    - port: 53
      protocol: TCP
```
Explanation:

Target Pods:
This policy applies to all pods in the default namespace with the label name: internal.

Ingress:
All incoming traffic is allowed to these pods. This is typically needed for UI-based testing during labs.

In production, you should restrict ingress to only trusted sources.

Egress:
Outbound traffic is restricted to:

Pods labeled name: mysql on TCP port 3306 (database service)
Pods labeled name: payroll on TCP port 8080 (payroll service)
Any destination on UDP/TCP port 53 (for DNS resolution, required for service discovery in Kubernetes)
DNS Access:
DNS is handled by the kube-dns service, which listens on port 53 for both UDP and TCP:

root@controlplane:~> kubectl get svc -n kube-system 
NAME       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
kube-dns   ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   18m

root@controlplane:~>
