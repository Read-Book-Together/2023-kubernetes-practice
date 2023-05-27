```
controlplane ~ ➜  kubectl get networkpolicy
NAME             POD-SELECTOR   AGE
payroll-policy   name=payroll   29s

controlplane ~ ➜  kubectl get pod
NAME       READY   STATUS    RESTARTS   AGE
external   1/1     Running   0          49s
internal   1/1     Running   0          49s
mysql      1/1     Running   0          49s
payroll    1/1     Running   0          49s
```

```
controlplane ~ ➜  kubectl describe networkpolicy payroll-policy
Name:         payroll-policy
Namespace:    default
Created on:   2023-05-27 08:42:41 -0400 EDT
Labels:       <none>
Annotations:  <none>
Spec:
  PodSelector:     name=payroll
  Allowing ingress traffic:
    To Port: 8080/TCP
    From:
      PodSelector: name=internal
  Not affecting egress traffic
  Policy Types: Ingress
```


```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: internal-policy
  namespace: default
spec:
  egress:
  - to:
    - podSelector:
        matchLabels:
          name: payroll
    ports:
    - port: 8080
      protocol: TCP
  - to:
    - podSelector:
        matchLabels:
          name: mysql
    ports:
    - port: 3306
      protocol: TCP
  podSelector:
    matchLabels:
      name: intenal
  policyTypes:
  - Egress
```

