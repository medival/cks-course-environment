#### Create sidecar proxy

Adding securityContext

```
securityContext:
       capabilities:
         add: ["NET_ADMIN"]
```

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: app
  name: app
spec:
  containers:
  - command:
    - sh
    - -c
    - ping www.google.com
    image: bash
    name: app
    resources: {}
  - name: proxy
    image: ubuntu
    command:
    - sh
    - -c
    - 'apt-get update && apt-get install iptables -y && iptables -L && sleep 1d'
    securityContext:
       capabilities:
         add: ["NET_ADMIN"]
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```
