```
controlplane ~ ➜  kubectl get sc
NAME                        PROVISIONER                     RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
local-path (default)        rancher.io/local-path           Delete          WaitForFirstConsumer   false                  11m
```

```
controlplane ~ ➜  kubectl get sc
NAME                        PROVISIONER                     RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
local-path (default)        rancher.io/local-path           Delete          WaitForFirstConsumer   false                  11m
local-storage               kubernetes.io/no-provisioner    Delete          WaitForFirstConsumer   false                  2s
portworx-io-priority-high   kubernetes.io/portworx-volume   Delete          Immediate              false                  2s
```

```
controlplane ~ ➜  kubectl describe sc
Name:                  local-path
IsDefaultClass:        Yes
Annotations:           objectset.rio.cattle.io/applied=H4sIAAAAAAAA/4yRT+vUMBCGv4rMua1bu1tKwIOu7EUEQdDzNJlux6aZkkwry7LfXbIqrIffn2PyZN7hfXIFXPg7xcQSwEBSiXimaupSxfJ2q6GAiYMDA9/+oKPHlKCAmRQdKoK5AoYgisoSUj5K/5OsJtIqslQWVT3lNM4xUDzJ5VegWJ63CQxMTXogW128+czBvf/gnIQXIwLOBAa8WPTl30qvGkoL2jw5rT2V6ZKUZij+SbG5eZVRDKR0F8SpdDTg6rW8YzCgcSW4FeCxJ/+sjxHTCAbqrhmag20Pw9DbZtfu210z7JuhPnQ719m2w3cOe7fPof81W1DHfLlE2Th/IEUwEDHYkWJe8PCsgJgL8PxVPNsLGPhEnjRr2cSvM33k4Dicv4jLC34g60niiWPSo4S0zhTh9jsAAP//ytgh5S0CAAA,objectset.rio.cattle.io/id=,objectset.rio.cattle.io/owner-gvk=k3s.cattle.io/v1, Kind=Addon,objectset.rio.cattle.io/owner-name=local-storage,objectset.rio.cattle.io/owner-namespace=kube-system,storageclass.kubernetes.io/is-default-class=true
Provisioner:           rancher.io/local-path
Parameters:            <none>
AllowVolumeExpansion:  <unset>
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     WaitForFirstConsumer
Events:                <none>


Name:            local-storage
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"storage.k8s.io/v1","kind":"StorageClass","metadata":{"annotations":{},"name":"local-storage"},"provisioner":"kubernetes.io/no-provisioner","volumeBindingMode":"WaitForFirstConsumer"}

Provisioner:           kubernetes.io/no-provisioner
Parameters:            <none>
AllowVolumeExpansion:  <unset>
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     WaitForFirstConsumer
Events:                <none>


Name:            portworx-io-priority-high
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"storage.k8s.io/v1","kind":"StorageClass","metadata":{"annotations":{},"name":"portworx-io-priority-high"},"parameters":{"priority_io":"high","repl":"1","snap_interval":"70"},"provisioner":"kubernetes.io/portworx-volume"}

Provisioner:           kubernetes.io/portworx-volume
Parameters:            priority_io=high,repl=1,snap_interval=70
AllowVolumeExpansion:  <unset>
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     Immediate
Events:                <none>
```

```
controlplane ~ ➜  kubectl get pvc
No resources found in default namespace.

controlplane ~ ➜  kubectl get pv
NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS    REASON   AGE
local-pv   500Mi      RWO            Retain           Available           local-storage            2m44s
```

```
controlplane ~ ➜  kubectl apply -f pvc.yaml 
persistentvolumeclaim/local-pvc created

controlplane ~ ➜  cat pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: local-pvc
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: local-storage
  resources:
    requests:
      storage: 500Mi
```

```
controlplane ~ ➜  kubectl events
LAST SEEN           TYPE      REASON                    OBJECT                            MESSAGE
17m                 Normal    Starting                  Node/controlplane                 Starting kubelet.
17m                 Warning   InvalidDiskCapacity       Node/controlplane                 invalid capacity 0 on image filesystem
17m (x2 over 17m)   Normal    NodeHasSufficientMemory   Node/controlplane                 Node controlplane status is now: NodeHasSufficientMemory
17m (x2 over 17m)   Normal    NodeHasNoDiskPressure     Node/controlplane                 Node controlplane status is now: NodeHasNoDiskPressure
17m (x2 over 17m)   Normal    NodeHasSufficientPID      Node/controlplane                 Node controlplane status is now: NodeHasSufficientPID
17m                 Normal    NodeAllocatableEnforced   Node/controlplane                 Updated Node Allocatable limit across pods
17m                 Normal    NodeReady                 Node/controlplane                 Node controlplane status is now: NodeReady
17m                 Normal    Starting                  Node/controlplane                 
17m                 Normal    Synced                    Node/controlplane                 Node synced successfully
17m                 Normal    RegisteredNode            Node/controlplane                 Node controlplane event: Registered Node controlplane in Controller
2s (x4 over 38s)    Normal    WaitForFirstConsumer      PersistentVolumeClaim/local-pvc   waiting for first consumer to be created before binding

```

```
controlplane ~ ➜  kubectl run nginx --image=nginx:alpine --dry-run=client -o yaml > pod.yaml
controlplane ~ ➜  kubectl apply -f pod.yaml
pod/nginx created

controlplane ~ ➜  kubectl get pod
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          8s

controlplane ~ ➜  cat pod.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx:alpine
    name: nginx
    resources: {}
    volumeMounts:
    - mountPath: "/var/www/html"
      name: local-volume
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  volumes:
    - name: local-volume
      persistentVolumeClaim:
        claimName: local-pvc
status: {}
```

```
controlplane ~ ➜  kubectl get pvc
NAME        STATUS   VOLUME     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
local-pvc   Bound    local-pv   500Mi      RWO            local-storage   5m15s
```

```
controlplane ~ ➜  kubectl get sc portworx-io-priority-high -o yaml > sc.yaml
controlplane ~ ➜  kubectl apply -f sc.yaml 
storageclass.storage.k8s.io/delayed-volume-sc created

controlplane ~ ➜  cat sc.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  creationTimestamp: "2023-06-04T12:16:32Z"
  name: delayed-volume-sc
provisioner: kubernetes.io/no-provisioner
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```
