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


