```
controlplane ~ ✖ kubectl exec ubuntu-sleeper -- id
uid=0(root) gid=0(root) groups=0(root)

controlplane ~ ✖ kubectl edit pod ubuntu-sleeper 
spec:
	continers:
		securityContext:
      		runAsUser: 1010
```

```
controlplane ~ ➜  cat multi-pod.yaml 
apiVersion: v1
kind: Pod
metadata:
  name: multi-pod
spec:
  securityContext:
    runAsUser: 1001
  containers:
  -  image: ubuntu
     name: web
     command: ["sleep", "5000"]
     securityContext:
      runAsUser: 1002

  -  image: ubuntu
     name: sidecar
     command: ["sleep", "5000"]
```

```
spec:
  containers:
    securityContext:
      capabilities:
        add: ["SYS_TIME", "NET_ADMIN"]
```