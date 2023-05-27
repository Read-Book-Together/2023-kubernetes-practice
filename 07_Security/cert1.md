```
openssl x509 -in ~~~ -text -noout
```

```
cat /etc/kubernetes/manifest/etcd.yaml
```
- /etc/kubernetes/pki/etcd/server-certificate.crt
- /etc/kubernetes/pki/etcd/server.crt

```
crictl ps -a
crictl logs container-id
```

```
cat /etc/kubernetes/manifest/kuba-apiserver.yaml
systemctl restart kubelet
```
- /etc/kubernetes/pki/ca.crt
- /etc/kubernetes/pki/etcd/ca.crt

