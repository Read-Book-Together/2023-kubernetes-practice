```
controlplane ~ ➜  kubectl get roles
No resources found in default namespace.

controlplane ~ ➜  kubectl get roles -A
NAMESPACE     NAME                                             CREATED AT
blue          developer                                        2023-05-20T08:52:57Z
kube-public   kubeadm:bootstrap-signer-clusterinfo             2023-05-20T08:42:53Z
kube-public   system:controller:bootstrap-signer               2023-05-20T08:42:51Z
kube-system   extension-apiserver-authentication-reader        2023-05-20T08:42:51Z
kube-system   kube-proxy                                       2023-05-20T08:42:55Z
kube-system   kubeadm:kubelet-config                           2023-05-20T08:42:52Z
kube-system   kubeadm:nodes-kubeadm-config                     2023-05-20T08:42:51Z
kube-system   system::leader-locking-kube-controller-manager   2023-05-20T08:42:51Z
kube-system   system::leader-locking-kube-scheduler            2023-05-20T08:42:51Z
kube-system   system:controller:bootstrap-signer               2023-05-20T08:42:51Z
kube-system   system:controller:cloud-provider                 2023-05-20T08:42:51Z
kube-system   system:controller:token-cleaner                  2023-05-20T08:42:51Z

controlplane ~ ➜  kubectl get roles -A | wc -l
13

controlplane ~ ➜  kubectl describe role kube-proxy -n kube-system
Name:         kube-proxy
Labels:       <none>
Annotations:  <none>
PolicyRule:
  Resources   Non-Resource URLs  Resource Names  Verbs
  ---------   -----------------  --------------  -----
  configmaps  []                 [kube-proxy]    [get]

controlplane ~ ➜  kubectl get rolebinding -A
NAMESPACE     NAME                                                ROLE                                                  AGE
blue          dev-user-binding                                    Role/developer                                        5m15s
kube-public   kubeadm:bootstrap-signer-clusterinfo                Role/kubeadm:bootstrap-signer-clusterinfo             15m
kube-public   system:controller:bootstrap-signer                  Role/system:controller:bootstrap-signer               15m
kube-system   kube-proxy                                          Role/kube-proxy                                       15m
kube-system   kubeadm:kubelet-config                              Role/kubeadm:kubelet-config                           15m
kube-system   kubeadm:nodes-kubeadm-config                        Role/kubeadm:nodes-kubeadm-config                     15m
kube-system   system::extension-apiserver-authentication-reader   Role/extension-apiserver-authentication-reader        15m
kube-system   system::leader-locking-kube-controller-manager      Role/system::leader-locking-kube-controller-manager   15m
kube-system   system::leader-locking-kube-scheduler               Role/system::leader-locking-kube-scheduler            15m
kube-system   system:controller:bootstrap-signer                  Role/system:controller:bootstrap-signer               15m
kube-system   system:controller:cloud-provider                    Role/system:controller:cloud-provider                 15m
kube-system   system:controller:token-cleaner                     Role/system:controller:token-cleaner                  15m

controlplane ~ ➜  kubectl describe rolebinding kube-proxy -n kube-system
Name:         kube-proxy
Labels:       <none>
Annotations:  <none>
Role:
  Kind:  Role
  Name:  kube-proxy
Subjects:
  Kind   Name                                             Namespace
  ----   ----                                             ---------
  Group  system:bootstrappers:kubeadm:default-node-token

controlplane ~ ✖ kubectl auth can-i list pods --as dev-user
no
```

```
controlplane ~ ✖ kubectl create --help
Create a resource from a file or from stdin.

 JSON and YAML formats are accepted.

Examples:
  # Create a pod using the data in pod.json
  kubectl create -f ./pod.json
  
  # Create a pod based on the JSON passed into stdin
  cat pod.json | kubectl create -f -
  
  # Edit the data in registry.yaml in JSON then create the resource using the edited data
  kubectl create -f registry.yaml --edit -o json

Available Commands:
  clusterrole           Create a cluster role
  clusterrolebinding    Create a cluster role binding for a particular cluster role
  configmap             Create a config map from a local file, directory or literal value
  cronjob               Create a cron job with the specified name
  deployment            Create a deployment with the specified name
  ingress               Create an ingress with the specified name
  job                   Create a job with the specified name
  namespace             Create a namespace with the specified name
  poddisruptionbudget   Create a pod disruption budget with the specified name
  priorityclass         Create a priority class with the specified name
  quota                 Create a quota with the specified name
  role                  Create a role with single rule
  rolebinding           Create a role binding for a particular role or cluster role
  secret                Create a secret using specified subcommand
  service               Create a service using a specified subcommand
  serviceaccount        Create a service account with the specified name
  token                 Request a service account token
```

```
controlplane ~ ➜  kubectl create role developer --verb=list --resource=pods --dry-run=client -o yaml > developer_role.yaml
controlplane ~ ➜  kubectl create rolebinding dev-user-binding --role=developer --user=dev-user --dry-run=client -o yaml > dev-user-binding.yaml
```

```
controlplane ~ ➜  cat developer_role.yaml 
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  creationTimestamp: null
  name: developer
rules:
- apiGroups:
  - ""
  resources:
  - pods
  verbs:
  - list
  - create
  - delete
```

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  creationTimestamp: null
  name: dev-user-binding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: developer
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: dev-user
```

```
controlplane ~ ➜  kubectl get roles -n blue
NAME        CREATED AT
developer   2023-05-20T08:52:57Z

controlplane ~ ➜  kubectl get rolebinding -n blue
NAME               ROLE             AGE
dev-user-binding   Role/developer   15m
```

```
controlplane ~ ➜  kubectl describe role developer -n blue
Name:         developer
Labels:       <none>
Annotations:  <none>
PolicyRule:
  Resources  Non-Resource URLs  Resource Names  Verbs
  ---------  -----------------  --------------  -----
  pods       []                 [blue-app]      [get watch create delete]

controlplane ~ ➜  kubectl describe rolebinding dev-user-binding -n blue
Name:         dev-user-binding
Labels:       <none>
Annotations:  <none>
Role:
  Kind:  Role
  Name:  developer
Subjects:
  Kind  Name      Namespace
  ----  ----      ---------
  User  dev-user
```

```
controlplane ~ ➜  kubectl edit role developer -n blue
role.rbac.authorization.k8s.io/developer edited
```

```
controlplane ~ ✖ kubectl get role developer -n blue -o yaml > developer_blue.yaml
controlplane ~ ✖ kubectl get rolebindings -n blue -o yaml >dev-user-binding_blue.yaml
```

```
controlplane ~ ➜  kubectl replace --force -f developer_blue.yaml 
role.rbac.authorization.k8s.io "developer" deleted
role.rbac.authorization.k8s.io/developer replaced

controlplane ~ ➜  kubectl describe role developer -n blue
Name:         developer
Labels:       <none>
Annotations:  <none>
PolicyRule:
  Resources         Non-Resource URLs  Resource Names   Verbs
  ---------         -----------------  --------------   -----
  deployments.apps  []                 []               [create]
  pods              []                 [dark-blue-app]  [get watch create delete]
```

