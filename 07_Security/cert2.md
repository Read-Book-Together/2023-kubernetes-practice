1. 
```
A new member akshay joined our team. He requires access to our cluster. The Certificate Signing Request is at the /root location.


Inspect it
```

2.
```
Create a CertificateSigningRequest object with the name akshay with the contents of the akshay.csr file


As of kubernetes 1.19, the API to use for CSR is certificates.k8s.io/v1.

Please note that an additional field called signerName should also be added when creating CSR. For client authentication to the API server we will use the built-in signer kubernetes.io/kube-apiserver-client.
```
```
cat akshay.csr | base64 | tr -d "\n"
kubectl apply -f akshay.yaml
kubectl get csr
```
```
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: akshay
spec:
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZqQ0NBVDRDQVFBd0VURVBNQTBHQTFVRUF3d0dZV3R6YUdGNU1JSUJJakFOQmdrcWhraUc5dzBCQVFFRgpBQU9DQVE4QU1JSUJDZ0tDQVFFQW9PV200OWZMZ0F3VnRGM1k4Njd5S2thWFVvcFRxd1hLSTZvMU02cXJCY2NhCmRKdU15ZDhOTlp5dlNwQjB0dWJQNlFVNVd2NnZ1bjFaRDdUWHA5Y1dPN1Y5RlNublFYbnFyZThvOGN2UUpuTngKd2c4TnlpYzQrWGdEYXBWeUxTSlRYdkJKQUJDNTU5QWtoa2JoVmFoMmtObHF4ZEwrU0VVdzczN29GZUQzQWtJYQpRaS96UzNFcGZkSmhUdGVuc2dhNUphQzRkV0Qyd1JIS3VPNjBVc3Nxc1EzeGhnQXN3TEdFSHh2Qms4Q3NoVkp2CjRVKzhuRGtUVGwwN3RpM2V5aXRoSHJSK2tPTW9mQ01LN0V1cnVtclBUSE5SanBadVVFV2owTjhVTVlubFhMOUEKR2VuWnJjbmtDcmI4QXJiQkwrSzE3Q0FMdTYvM2x0R0ROTzQ0M1M4Vit3SURBUUFCb0FBd0RRWUpLb1pJaHZjTgpBUUVMQlFBRGdnRUJBR1JKTjZTc20zSHVWQllZN3NsNjVlMUZZdEU4aHRlN2NRaXFWRVA2Q24wRS8zMk83OHUwCkpDVjFSNXdIWHYvc1pMaVpSREd1YUp5VXFVU3cxdUNON3Zvc05keEVEdkdvc0UxdndlQ1hoUXJiSU5CV0lqYXMKdTgzVnZFaHRDTytrQzV5L3V1Z0x4SmhiL2dkMS9pR3JienVHb3FGQitYbjJqTjBReGlyQUN0V3Z0R3pRZkVpQwovNzlPcnRBWW5zVG1JYzJFMnVxNXFaOUxlVnZQTHpjb1p3ZEJhL21QK3NxSmg4MkJ6UDUxOFc3V1pzNnJ6SjRaCnZBRExwU2lyTUhmdlFRVkRZM0U4MzlxbDJtVTZzOThGalhkaFRZMUhZT0NTY0VxYVorbDJBRGYxWTFDc1hrRnQKdGNmWXdZR1Joc2ExWjlNNDBPcFQ4MUxFbWlBVFR2bXFZd1U9Ci0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=
  signerName: kubernetes.io/kube-apiserver-client
  expirationSeconds: 86400
  usages:
  - client auth
```


controlplane ~ ➜  kubectl get csr
NAME        AGE     SIGNERNAME                                    REQUESTOR                  REQUESTEDDURATION   CONDITION
akshay      88s     kubernetes.io/kube-apiserver-client           kubernetes-admin           24h                 Pending
csr-wqz2w   7m32s   kubernetes.io/kube-apiserver-client-kubelet   system:node:controlplane   <none>              Approved,Issued

controlplane ~ ➜  kubectl certificate approve akshay
certificatesigningrequest.certificates.k8s.io/akshay approved

controlplane ~ ➜  kubectl get pod
No resources found in default namespace.

controlplane ~ ➜  kubectl get csr
NAME        AGE     SIGNERNAME                                    REQUESTOR                  REQUESTEDDURATION   CONDITION
akshay      115s    kubernetes.io/kube-apiserver-client           kubernetes-admin           24h                 Approved,Issued
csr-wqz2w   7m59s   kubernetes.io/kube-apiserver-client-kubelet   system:node:controlplane   <none>              Approved,Issued

controlplane ~ ➜  kubectl describe csr
Name:         akshay
Labels:       <none>
Annotations:  kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"certificates.k8s.io/v1","kind":"CertificateSigningRequest","metadata":{"annotations":{},"name":"akshay"},"spec":{"expirationSeconds":86400,"request":"LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZqQ0NBVDRDQVFBd0VURVBNQTBHQTFVRUF3d0dZV3R6YUdGNU1JSUJJakFOQmdrcWhraUc5dzBCQVFFRgpBQU9DQVE4QU1JSUJDZ0tDQVFFQW9PV200OWZMZ0F3VnRGM1k4Njd5S2thWFVvcFRxd1hLSTZvMU02cXJCY2NhCmRKdU15ZDhOTlp5dlNwQjB0dWJQNlFVNVd2NnZ1bjFaRDdUWHA5Y1dPN1Y5RlNublFYbnFyZThvOGN2UUpuTngKd2c4TnlpYzQrWGdEYXBWeUxTSlRYdkJKQUJDNTU5QWtoa2JoVmFoMmtObHF4ZEwrU0VVdzczN29GZUQzQWtJYQpRaS96UzNFcGZkSmhUdGVuc2dhNUphQzRkV0Qyd1JIS3VPNjBVc3Nxc1EzeGhnQXN3TEdFSHh2Qms4Q3NoVkp2CjRVKzhuRGtUVGwwN3RpM2V5aXRoSHJSK2tPTW9mQ01LN0V1cnVtclBUSE5SanBadVVFV2owTjhVTVlubFhMOUEKR2VuWnJjbmtDcmI4QXJiQkwrSzE3Q0FMdTYvM2x0R0ROTzQ0M1M4Vit3SURBUUFCb0FBd0RRWUpLb1pJaHZjTgpBUUVMQlFBRGdnRUJBR1JKTjZTc20zSHVWQllZN3NsNjVlMUZZdEU4aHRlN2NRaXFWRVA2Q24wRS8zMk83OHUwCkpDVjFSNXdIWHYvc1pMaVpSREd1YUp5VXFVU3cxdUNON3Zvc05keEVEdkdvc0UxdndlQ1hoUXJiSU5CV0lqYXMKdTgzVnZFaHRDTytrQzV5L3V1Z0x4SmhiL2dkMS9pR3JienVHb3FGQitYbjJqTjBReGlyQUN0V3Z0R3pRZkVpQwovNzlPcnRBWW5zVG1JYzJFMnVxNXFaOUxlVnZQTHpjb1p3ZEJhL21QK3NxSmg4MkJ6UDUxOFc3V1pzNnJ6SjRaCnZBRExwU2lyTUhmdlFRVkRZM0U4MzlxbDJtVTZzOThGalhkaFRZMUhZT0NTY0VxYVorbDJBRGYxWTFDc1hrRnQKdGNmWXdZR1Joc2ExWjlNNDBPcFQ4MUxFbWlBVFR2bXFZd1U9Ci0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=","signerName":"kubernetes.io/kube-apiserver-client","usages":["client auth"]},"status":{}}

CreationTimestamp:   Thu, 18 May 2023 07:10:51 -0400
Requesting User:     kubernetes-admin
Signer:              kubernetes.io/kube-apiserver-client
Requested Duration:  24h
Status:              Approved,Issued
Subject:
         Common Name:    akshay
         Serial Number:  
Events:  <none>


Name:               csr-wqz2w
Labels:             <none>
Annotations:        <none>
CreationTimestamp:  Thu, 18 May 2023 07:04:47 -0400
Requesting User:    system:node:controlplane
Signer:             kubernetes.io/kube-apiserver-client-kubelet
Status:             Approved,Issued
Subject:
         Common Name:    system:node:controlplane
         Serial Number:  
         Organization:   system:nodes
Events:  <none>

controlplane ~ ➜  kubectl get csr -o wide
NAME        AGE     SIGNERNAME                                    REQUESTOR                  REQUESTEDDURATION   CONDITION
akshay      2m35s   kubernetes.io/kube-apiserver-client           kubernetes-admin           24h                 Approved,Issued
csr-wqz2w   8m39s   kubernetes.io/kube-apiserver-client-kubelet   system:node:controlplane   <none>              Approved,Issued

controlplane ~ ➜  kubectl certificate
Modify certificate resources.

Available Commands:
  approve       Approve a certificate signing request
  deny          Deny a certificate signing request

Usage:
  kubectl certificate SUBCOMMAND [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

controlplane ~ ➜  kubectl get pods -A
NAMESPACE      NAME                                   READY   STATUS    RESTARTS   AGE
kube-flannel   kube-flannel-ds-cfqlx                  1/1     Running   0          8m56s
kube-system    coredns-787d4945fb-72n4g               1/1     Running   0          8m56s
kube-system    coredns-787d4945fb-m4vxc               1/1     Running   0          8m56s
kube-system    etcd-controlplane                      1/1     Running   0          9m11s
kube-system    kube-apiserver-controlplane            1/1     Running   0          9m7s
kube-system    kube-controller-manager-controlplane   1/1     Running   0          9m7s
kube-system    kube-proxy-6fsv6                       1/1     Running   0          8m57s
kube-system    kube-scheduler-controlplane            1/1     Running   0          9m7s

controlplane ~ ➜  kubectl get csr -o wide
NAME          AGE     SIGNERNAME                                    REQUESTOR                  REQUESTEDDURATION   CONDITION
agent-smith   9s      kubernetes.io/kube-apiserver-client           agent-x                    <none>              Pending
akshay        3m45s   kubernetes.io/kube-apiserver-client           kubernetes-admin           24h                 Approved,Issued
csr-wqz2w     9m49s   kubernetes.io/kube-apiserver-client-kubelet   system:node:controlplane   <none>              Approved,Issued

controlplane ~ ➜  ls
akshay.csr  akshay.key  akshay.yaml

controlplane ~ ➜  kubectl describe csr agent-smith
Name:               agent-smith
Labels:             <none>
Annotations:        <none>
CreationTimestamp:  Thu, 18 May 2023 07:14:27 -0400
Requesting User:    agent-x
Signer:             kubernetes.io/kube-apiserver-client
Status:             Pending
Subject:
         Common Name:    new-user
         Serial Number:  
Events:  <none>

controlplane ~ ➜  kubectl get csr
NAME          AGE   SIGNERNAME                                    REQUESTOR                  REQUESTEDDURATION   CONDITION
agent-smith   84s   kubernetes.io/kube-apiserver-client           agent-x                    <none>              Pending
akshay        5m    kubernetes.io/kube-apiserver-client           kubernetes-admin           24h                 Approved,Issued
csr-wqz2w     11m   kubernetes.io/kube-apiserver-client-kubelet   system:node:controlplane   <none>              Approved,Issued

controlplane ~ ➜  kubectl certificate deny agent-smith
certificatesigningrequest.certificates.k8s.io/agent-smith denied

controlplane ~ ➜  kubectl get csr
NAME          AGE     SIGNERNAME                                    REQUESTOR                  REQUESTEDDURATION   CONDITION
agent-smith   98s     kubernetes.io/kube-apiserver-client           agent-x                    <none>              Denied
akshay        5m14s   kubernetes.io/kube-apiserver-client           kubernetes-admin           24h                 Approved,Issued
csr-wqz2w     11m     kubernetes.io/kube-apiserver-client-kubelet   system:node:controlplane   <none>              Approved,Issued

controlplane ~ ➜  kubectl delete csr agent-smith
certificatesigningrequest.certificates.k8s.io "agent-smith" deleted