```
controlplane ~ ➜  kubectl get pods
NAME     READY   STATUS    RESTARTS   AGE
webapp   1/1     Running   0          14s


controlplane ~ ✖ kubectl describe pod webapp
Name:             webapp
Namespace:        default
Priority:         0
Service Account:  default
Node:             controlplane/192.15.171.3
Start Time:       Sun, 04 Jun 2023 07:21:33 -0400
Labels:           <none>
Annotations:      <none>
Status:           Running
IP:               10.244.0.4
IPs:
  IP:  10.244.0.4
Containers:
  event-simulator:
    Container ID:   containerd://150acc4065574a422172fa50f6910759221708155a3bb8d77cd4e736010a54cb
    Image:          kodekloud/event-simulator
    Image ID:       docker.io/kodekloud/event-simulator@sha256:1e3e9c72136bbc76c96dd98f29c04f298c3ae241c7d44e2bf70bcc209b030bf9
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Sun, 04 Jun 2023 07:21:39 -0400
    Ready:          True
    Restart Count:  0
    Environment:
      LOG_HANDLERS:  file
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-h57rt (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-h57rt:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  72s   default-scheduler  Successfully assigned default/webapp to controlplane
  Normal  Pulling    71s   kubelet            Pulling image "kodekloud/event-simulator"
  Normal  Pulled     67s   kubelet            Successfully pulled image "kodekloud/event-simulator" in 4.056936492s (4.05695541s including waiting)
  Normal  Created    66s   kubelet            Created container event-simulator
  Normal  Started    66s   kubelet            Started container event-simulator

```

```
controlplane ~ ➜  kubectl exec webapp -- cat /log/app.log
[2023-06-04 11:21:39,588] INFO in event-simulator: USER1 is viewing page1
[2023-06-04 11:21:40,589] INFO in event-simulator: USER1 is viewing page1
[2023-06-04 11:21:41,590] INFO in event-simulator: USER1 logged in
[2023-06-04 11:21:42,591] INFO in event-simulator: USER2 is viewing page3
[2023-06-04 11:21:43,592] INFO in event-simulator: USER2 logged out
[2023-06-04 11:21:44,593] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2023-06-04 11:21:44,593] INFO in event-simulator: USER4 logged out
[2023-06-04 11:21:45,595] INFO in event-simulator: USER2 is viewing page2
[2023-06-04 11:21:46,596] INFO in event-simulator: USER4 logged out
[2023-06-04 11:21:47,597] WARNING in event-simulator: USER7 Order failed as the item is OUT OF STOCK.
[2023-06-04 11:21:47,598] INFO in event-simulator: USER2 is viewing page3
[2023-06-04 11:21:48,599] INFO in event-simulator: USER3 is viewing page2
[2023-06-04 11:21:49,599] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2023-06-04 11:21:49,600] INFO in event-simulator: USER4 is viewing page1
[2023-06-04 11:21:50,601] INFO in event-simulator: USER1 is viewing page2
[2023-06-04 11:21:51,603] INFO in event-simulator: USER1 logged out
[2023-06-04 11:21:52,604] INFO in event-simulator: USER4 logged out
[2023-06-04 11:21:53,605] INFO in event-simulator: USER3 logged in
[2023-06-04 11:21:54,605] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2023-06-04 11:21:54,605] INFO in event-simulator: USER1 is viewing page2
[2023-06-04 11:21:55,607] WARNING in event-simulator: USER7 Order failed as the item is OUT OF STOCK.
[2023-06-04 11:21:55,607] INFO in event-simulator: USER3 is viewing page3
[2023-06-04 11:21:56,608] INFO in event-simulator: USER3 is viewing page2
[2023-06-04 11:21:57,610] INFO in event-simulator: USER2 is viewing page3
[2023-06-04 11:21:58,611] INFO in event-simulator: USER2 logged out
[2023-06-04 11:21:59,612] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2023-06-04 11:21:59,613] INFO in event-simulator: USER1 is viewing page3
[2023-06-04 11:22:00,614] INFO in event-simulator: USER2 is viewing page2
[2023-06-04 11:22:01,614] INFO in event-simulator: USER4 is viewing page3
[2023-06-04 11:22:02,616] INFO in event-simulator: USER3 is viewing page3
[2023-06-04 11:22:03,617] WARNING in event-simulator: USER7 Order failed as the item is OUT OF STOCK.
[2023-06-04 11:22:03,617] INFO in event-simulator: USER1 logged in
[2023-06-04 11:22:04,618] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2023-06-04 11:22:04,619] INFO in event-simulator: USER4 logged in
[2023-06-04 11:22:05,620] INFO in event-simulator: USER2 logged in
[2023-06-04 11:22:06,621] INFO in event-simulator: USER3 is viewing page1
[2023-06-04 11:22:07,623] INFO in event-simulator: USER3 logged out
```

```
controlplane ~ ➜  cat /tmp/kubectl-edit-291989344.yaml 
# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
# pods "webapp" was not valid:
# * spec.containers[0].volumeMounts[1].name: Not found: "webapp-log-access"
# * spec: Forbidden: pod updates may not change fields other than `spec.containers[*].image`, `spec.initContainers[*].image`, `spec.activeDeadlineSeconds`, `spec.tolerations` (only additions to existing tolerations) or `spec.terminationGracePeriodSeconds` (allow it to be set to 1 if it was previously negative)
#   core.PodSpec{
#       Volumes: []core.Volume{
#               {Name: "kube-api-access-h57rt", VolumeSource: {Projected: &{Sources: {{ServiceAccountToken: &{ExpirationSeconds: 3607, Path: "token"}}, {ConfigMap: &{LocalObjectReference: {Name: "kube-root-ca.crt"}, Items: {{Key: "ca.crt", Path: "ca.crt"}}}}, {DownwardAPI: &{Items: {{Path: "namespace", FieldRef: &{APIVersion: "v1", FieldPath: "metadata.namespace"}}}}}}, DefaultMode: &420}}},
# +             {
# +                     Name:         "webapp-log",
# +                     VolumeSource: core.VolumeSource{HostPath: &core.HostPathVolumeSource{Path: "/var/log/webapp", Type: &""}},
# +             },
#       },
#       InitContainers: nil,
#       Containers: []core.Container{
#               {
#                       ... // 7 identical fields
#                       Env:       {{Name: "LOG_HANDLERS", Value: "file"}},
#                       Resources: {},
#                       VolumeMounts: []core.VolumeMount{
#                               {Name: "kube-api-access-h57rt", ReadOnly: true, MountPath: "/var/run/secrets/kubernetes.io/serviceaccount"},
# +                             {Name: "webapp-log-access", MountPath: "/log"},
#                       },
#                       VolumeDevices: nil,
#                       LivenessProbe: nil,
#                       ... // 10 identical fields
#               },
#       },
#       EphemeralContainers: nil,
#       RestartPolicy:       "Always",
#       ... // 28 identical fields
#   }

#
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2023-06-04T11:21:33Z"
  name: webapp
  namespace: default
  resourceVersion: "800"
  uid: e74ffb29-c635-4280-919f-7a8cf56f6c78
spec:
  containers:
  - env:
    - name: LOG_HANDLERS
      value: file
    image: kodekloud/event-simulator
    imagePullPolicy: Always
    name: event-simulator
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-h57rt
      readOnly: true
    - mountPath: /log
      name: webapp-log
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: controlplane
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: kube-api-access-h57rt
    projected:
      defaultMode: 420
      sources:
      - serviceAccountToken:
          expirationSeconds: 3607
          path: token
      - configMap:
          items:
          - key: ca.crt
            path: ca.crt
          name: kube-root-ca.crt
      - downwardAPI:
          items:
          - fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
            path: namespace
  - name: webapp-log
    hostPath:
      path: /var/log/webapp
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2023-06-04T11:21:33Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2023-06-04T11:21:39Z"
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2023-06-04T11:21:39Z"
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2023-06-04T11:21:33Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: containerd://150acc4065574a422172fa50f6910759221708155a3bb8d77cd4e736010a54cb
    image: docker.io/kodekloud/event-simulator:latest
    imageID: docker.io/kodekloud/event-simulator@sha256:1e3e9c72136bbc76c96dd98f29c04f298c3ae241c7d44e2bf70bcc209b030bf9
    lastState: {}
    name: event-simulator
    ready: true
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2023-06-04T11:21:39Z"
  hostIP: 192.15.171.3
  phase: Running
  podIP: 10.244.0.4
  podIPs:
  - ip: 10.244.0.4
  qosClass: BestEffort
  startTime: "2023-06-04T11:21:33Z"

controlplane ~ ➜  kubectl replace --force -f /tmp/kubectl-edit-291989344.yaml 
```

```
controlplane ~ ➜  kubectl apply -f pv.yaml 
persistentvolume/pv-log created

controlplane ~ ➜  cat pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-log
spec:
  accessModes: 
  - ReadWriteMany
  hostPath:
    path: /pv/log
  persistentVolumeReclaimPolicy: Retain
  capacity:
    storage: 100Mi
```

```
controlplane ~ ➜  kubectl apply -f pvc.yaml
persistentvolumeclaim/claim-log-1 created

controlplane ~ ➜  cat pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: claim-log-1
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 50Mi
  volumeName: pv-log
```

```
controlplane ~ ✖ kubectl get pvc
NAME          STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
claim-log-1   Pending   pv-log   0 
```

```
controlplane ~ ➜  kubectl get pv
NAME     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
pv-log   100Mi      RWX            Retain           Available                                   3m24s

controlplane ~ ➜  kubectl describe pv
Name:            pv-log
Labels:          <none>
Annotations:     <none>
Finalizers:      [kubernetes.io/pv-protection]
StorageClass:    
Status:          Available
Claim:           
Reclaim Policy:  Retain
Access Modes:    RWX
VolumeMode:      Filesystem
Capacity:        100Mi
Node Affinity:   <none>
Message:         
Source:
    Type:          HostPath (bare host directory volume)
    Path:          /pv/log
    HostPathType:  
Events:            <none>
```

```
controlplane ~ ✖ kubectl events
LAST SEEN           TYPE      REASON                    OBJECT                              MESSAGE
23m (x8 over 23m)   Normal    NodeHasSufficientMemory   Node/controlplane                   Node controlplane status is now: NodeHasSufficientMemory
23m (x7 over 23m)   Normal    NodeHasNoDiskPressure     Node/controlplane                   Node controlplane status is now: NodeHasNoDiskPressure
23m (x7 over 23m)   Normal    NodeHasSufficientPID      Node/controlplane                   Node controlplane status is now: NodeHasSufficientPID
22m                 Normal    Starting                  Node/controlplane                   Starting kubelet.
22m                 Warning   InvalidDiskCapacity       Node/controlplane                   invalid capacity 0 on image filesystem
22m                 Normal    NodeHasSufficientPID      Node/controlplane                   Node controlplane status is now: NodeHasSufficientPID
22m                 Normal    NodeAllocatableEnforced   Node/controlplane                   Updated Node Allocatable limit across pods
22m                 Normal    NodeHasSufficientMemory   Node/controlplane                   Node controlplane status is now: NodeHasSufficientMemory
22m                 Normal    NodeHasNoDiskPressure     Node/controlplane                   Node controlplane status is now: NodeHasNoDiskPressure
22m                 Normal    RegisteredNode            Node/controlplane                   Node controlplane event: Registered Node controlplane in Controller
22m                 Normal    Starting                  Node/controlplane                   
22m                 Normal    NodeReady                 Node/controlplane                   Node controlplane status is now: NodeReady
18m                 Normal    Scheduled                 Pod/webapp                          Successfully assigned default/webapp to controlplane
18m                 Normal    Pulling                   Pod/webapp                          Pulling image "kodekloud/event-simulator"
18m                 Normal    Pulled                    Pod/webapp                          Successfully pulled image "kodekloud/event-simulator" in 4.056936492s (4.05695541s including waiting)
18m                 Normal    Created                   Pod/webapp                          Created container event-simulator
18m                 Normal    Started                   Pod/webapp                          Started container event-simulator
12m (x2 over 12m)   Normal    Killing                   Pod/webapp                          Stopping container event-simulator
11m                 Normal    Pulling                   Pod/webapp                          Pulling image "kodekloud/event-simulator"
11m                 Normal    Pulled                    Pod/webapp                          Successfully pulled image "kodekloud/event-simulator" in 292.808415ms (292.819388ms including waiting)
11m                 Normal    Created                   Pod/webapp                          Created container event-simulator
11m                 Normal    Started                   Pod/webapp                          Started container event-simulator
1s (x8 over 97s)    Warning   VolumeMismatch            PersistentVolumeClaim/claim-log-1   Cannot bind to requested volume "pv-log": incompatible accessMode
```

```
controlplane ~ ➜  kubectl replace --force -f pvc.yaml 
persistentvolumeclaim "claim-log-1" deleted
persistentvolumeclaim/claim-log-1 replaced

controlplane ~ ➜  cat pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: claim-log-1
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 50Mi
  volumeName: pv-log

controlplane ~ ➜  kubectl get pvc
NAME          STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
claim-log-1   Bound    pv-log   100Mi      RWX                           24s
```

```
controlplane ~ ➜  kubectl replace --force -f /tmp/kubectl-edit-291989344.yaml 
pod "webapp" deleted
pod/webapp replaced

controlplane ~ ➜  cat /tmp/kubectl-edit-291989344.yaml 
# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
# pods "webapp" was not valid:
# * spec.containers[0].volumeMounts[1].name: Not found: "webapp-log-access"
# * spec: Forbidden: pod updates may not change fields other than `spec.containers[*].image`, `spec.initContainers[*].image`, `spec.activeDeadlineSeconds`, `spec.tolerations` (only additions to existing tolerations) or `spec.terminationGracePeriodSeconds` (allow it to be set to 1 if it was previously negative)
#   core.PodSpec{
#       Volumes: []core.Volume{
#               {Name: "kube-api-access-h57rt", VolumeSource: {Projected: &{Sources: {{ServiceAccountToken: &{ExpirationSeconds: 3607, Path: "token"}}, {ConfigMap: &{LocalObjectReference: {Name: "kube-root-ca.crt"}, Items: {{Key: "ca.crt", Path: "ca.crt"}}}}, {DownwardAPI: &{Items: {{Path: "namespace", FieldRef: &{APIVersion: "v1", FieldPath: "metadata.namespace"}}}}}}, DefaultMode: &420}}},
# +             {
# +                     Name:         "webapp-log",
# +                     VolumeSource: core.VolumeSource{HostPath: &core.HostPathVolumeSource{Path: "/var/log/webapp", Type: &""}},
# +             },
#       },
#       InitContainers: nil,
#       Containers: []core.Container{
#               {
#                       ... // 7 identical fields
#                       Env:       {{Name: "LOG_HANDLERS", Value: "file"}},
#                       Resources: {},
#                       VolumeMounts: []core.VolumeMount{
#                               {Name: "kube-api-access-h57rt", ReadOnly: true, MountPath: "/var/run/secrets/kubernetes.io/serviceaccount"},
# +                             {Name: "webapp-log-access", MountPath: "/log"},
#                       },
#                       VolumeDevices: nil,
#                       LivenessProbe: nil,
#                       ... // 10 identical fields
#               },
#       },
#       EphemeralContainers: nil,
#       RestartPolicy:       "Always",
#       ... // 28 identical fields
#   }

#
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2023-06-04T11:21:33Z"
  name: webapp
  namespace: default
  resourceVersion: "800"
  uid: e74ffb29-c635-4280-919f-7a8cf56f6c78
spec:
  containers:
  - env:
    - name: LOG_HANDLERS
      value: file
    image: kodekloud/event-simulator
    imagePullPolicy: Always
    name: event-simulator
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-h57rt
      readOnly: true
    - mountPath: /log
      name: webapp-log
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: controlplane
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: kube-api-access-h57rt
    projected:
      defaultMode: 420
      sources:
      - serviceAccountToken:
          expirationSeconds: 3607
          path: token
      - configMap:
          items:
          - key: ca.crt
            path: ca.crt
          name: kube-root-ca.crt
      - downwardAPI:
          items:
          - fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
            path: namespace
  - name: webapp-log
    persistentVolumeClaim:
      claimName: claim-log-1
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2023-06-04T11:21:33Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2023-06-04T11:21:39Z"
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2023-06-04T11:21:39Z"
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2023-06-04T11:21:33Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: containerd://150acc4065574a422172fa50f6910759221708155a3bb8d77cd4e736010a54cb
    image: docker.io/kodekloud/event-simulator:latest
    imageID: docker.io/kodekloud/event-simulator@sha256:1e3e9c72136bbc76c96dd98f29c04f298c3ae241c7d44e2bf70bcc209b030bf9
    lastState: {}
    name: event-simulator
    ready: true
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2023-06-04T11:21:39Z"
  hostIP: 192.15.171.3
  phase: Running
  podIP: 10.244.0.4
  podIPs:
  - ip: 10.244.0.4
  qosClass: BestEffort
  startTime: "2023-06-04T11:21:33Z"
```

```
controlplane ~ ➜  kubectl delete pvc claim-log-1
persistentvolumeclaim "claim-log-1" deleted

controlplane ~ ✖ kubectl get pvc
NAME          STATUS        VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
claim-log-1   Terminating   pv-log   100Mi      RWX                           5m31s
```

```
controlplane ~ ➜  kubectl delete pod webapp
pod "webapp" deleted

controlplane ~ ➜  kubectl get pvc
No resources found in default namespace.

controlplane ~ ➜  kubectl get pv
NAME     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS     CLAIM                 STORAGECLASS   REASON   AGE
pv-log   100Mi      RWX            Retain           Released   default/claim-log-1                           12m
```

