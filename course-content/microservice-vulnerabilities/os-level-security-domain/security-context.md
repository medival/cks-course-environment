#### Container no root permission

Add before container

```
securityContext:
  runAsUser: 1000
  runAsGroup: 3000
```

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: pod
  name: pod
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
  containers:
  - args:
    - sh
    - -c
    - sleep 1d
    image: busybox
    name: pod
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

#### Container non-root permission

Add before container

```
securityContext:
  runAsNonRoot: true
```

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: pod
  name: pod
spec:
  securityContext:
    runAsNonRoot: true
  containers:
  - args:
    - sh
    - -c
    - sleep 1d
    image: busybox
    name: pod
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

#### Force container run non-root

Add securityContext after container

```
securityContext:
  privileged: true
```

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: pod
  name: pod
spec:
  containers:
  - args:
    - sh
    - -c
    - sleep 1d
    image: busybox
    name: pod
    resources: {}
    securityContext:
      privileged: true
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

test it by exec to pods
`k exec -it pod -- sh `
`sysctl kernel.hostname=attacker`

#### Restricting escalation to root

Add securityContext after container

```
securityContext:
  allowPrivilegeEscalation: true
```

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: pod
  name: pod
spec:
  containers:
  - args:
    - sh
    - -c
    - sleep 1d
    image: busybox
    name: pod
    resources: {}
    securityContext:
      allowPrivilegeEscalation: false
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

verify by running
`k exec -it pod -- sh`
`cat /proc/1/status` // NoNewPrivilege=0 which mean safe
