### Hardening Cluster

`kubectl create sa accessor`
`kubectl run accessor --image nginx -oyaml > accessor.yaml`
`vi accessor.yaml`

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: accessor
  name: accessor
spec:
  serviceAccountName: accessor //add this
  containers:
  - image: nginx
    name: accessor
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

`kubectl apply -f accessor.yaml`
`kubectl exec -it accessor -- bash`

`# mount | grep sec`
`cd /run/secrets/kubernetes.io/serviceaccount`
`cat token`
`curl https://kubernete -k`
`curl https://kubernete -k -H "Authorization: token"`

### Disable SA mounting

`vi accessor.yaml`

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: accessor
  name: accessor
spec:
  automountServiceAccountToken: false //
  containers:
  - image: nginx
    name: accessor
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

`kubectl apply -f accessor.yaml replace --force`

### Limit SA using RBAC

`k get po`
`k auth can-i delete secrets --as system:serviceaccount:default:accessor`
`k create clusterrolebinding accessor --clusterrole edit --serviceaccount default:accessor`
`k auth can-i delete secrets --as system:serviceaccount:default:accessor`

Reference
https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin
https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account
