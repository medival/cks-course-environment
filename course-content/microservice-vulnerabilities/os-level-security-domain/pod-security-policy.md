#### Create PodSecurityPolicy to always enforce no allowPrivilegeEscalation

`ssh master`
`vi /etc/kubernetes/manifests/kube-apiserver.yaml`

Add PodSecurityPolicy to admission-plugins
`--enable-admission-plugins=NodeRestrictionl,PodSecurityPolicy`

`vi psp.yaml`

```
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:`
  name: default
spec:
  allowPrivilegeEscalation: false
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

`k create -f psp.yaml`
`k create deploy nginx --image nginx` <!-- Create deployment will be denied -->
`k run nginx --image nginx` <!-- Will works -->

#### Allowing SA default to create PSP by using RBAC

`k create role psp-access --verb=use --resource=podsecuritypolicies`
`k create rolebinding psp-access --role=psp-access --serviceAccount=default:default` <!-- default:default = namespaces -->

`k create deploy nginx --image nginx` <!-- Should be working -->
