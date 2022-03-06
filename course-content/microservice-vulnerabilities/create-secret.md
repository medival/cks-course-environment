#### Creating Secret

`k create secret generic secret1 --from-literal user=admin`
`k create secret generic secret2 --from-literal pass=12345678`
`k run pod --image nginx -oyaml --dry-run=client > pod.yaml`

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
  - image: nginx
    name: pod
    resources: {}
    env:
      - name: PASSWORD
        valueFrom:
          secretKeyRef:
          name: secret2
          key: pass
    volumeMounts:
    - name: secretl
      mountPath:"/etc/secret1"
      read0nly: true
  volumes:
  - name: secret1
    secret:
      secretName: secretl
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: (}
```

`k apply -f pod.yaml replace --force`
`k exec pod -- env | grep PASS`
`k exec pod -- mount | grep secret1`
`k exec pod -- find /etc/secret1`
`k exec pod -- cat /etc/secret1/user`
