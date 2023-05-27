```
kubectl create secret docker-registry ~~~ --docker-server=myprivateregistry:5000 --docker-username=dock_user --docker-password=dock_password --docker-email=dock_user@myprivateregistry:5000
```

```
kubectl edit deployment web
```

```
spec:
	imagePullSecrets:
	- name: private-reg-cred
```