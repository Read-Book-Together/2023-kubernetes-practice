```
controlplane ~ ➜  kubectl get clusterroles -A | wc -l
70

controlplane ~ ➜  kubectl get clusterrolebindings -A | wc -l
55

controlplane ~ ➜  kubectl describe clusterrole cluster-admin
Name:         cluster-admin
Labels:       kubernetes.io/bootstrapping=rbac-defaults
Annotations:  rbac.authorization.kubernetes.io/autoupdate: true
PolicyRule:
  Resources  Non-Resource URLs  Resource Names  Verbs
  ---------  -----------------  --------------  -----
  *.*        []                 []              [*]
             [*]                []              [*]

controlplane ~ ➜  kubectl describe clusterrolebindings cluster-admin
Name:         cluster-admin
Labels:       kubernetes.io/bootstrapping=rbac-defaults
Annotations:  rbac.authorization.kubernetes.io/autoupdate: true
Role:
  Kind:  ClusterRole
  Name:  cluster-admin
Subjects:
  Kind   Name            Namespace
  ----   ----            ---------
  Group  system:masters
```

```
controlplane ~ ➜  kubectl create clusterrole michelle-role --verb=* --resource=nodes
clusterrole.rbac.authorization.k8s.io/michelle-role created
controlplane ~ ➜  kubectl create clusterrolebinding michelle-rolebinding --user=michelle --clusterrole=michelle-role
clusterrolebinding.rbac.authorization.k8s.io/michelle-rolebinding created
```

```
controlplane ~ ✖ kubectl api-resources
controlplane ~ ➜  kubectl create clusterrole storage-admin --verb=* --resource=persistentvolumes,storageclasses
clusterrole.rbac.authorization.k8s.io/storage-admin created

controlplane ~ ➜  kubectl describe clusterrole storage-admin
Name:         storage-admin
Labels:       <none>
Annotations:  <none>
PolicyRule:
  Resources                      Non-Resource URLs  Resource Names  Verbs
  ---------                      -----------------  --------------  -----
  persistentvolumes              []                 []              [*]
  storageclasses.storage.k8s.io  []                 []              [*]
controlplane ~ ➜  kubectl create clusterrolebinding michelle-storage-admin --user=michelle --clusterrole=storage-admin
clusterrolebinding.rbac.authorization.k8s.io/michelle-storage-admin created
controlplane ~ ➜  kubectl describe clusterrolebinding michelle-storage-admin
Name:         michelle-storage-admin
Labels:       <none>
Annotations:  <none>
Role:
  Kind:  ClusterRole
  Name:  storage-admin
Subjects:
  Kind  Name      Namespace
  ----  ----      ---------
  User  michelle
```