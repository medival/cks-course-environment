#### Upgrade Kubernetes

1. Upgrade Control Plan/Master Component
   - Kube-api server
   - controll-manager
   - scheduler
2. Upgrade Worker Component
   - kubelet
   - kube-proxy
3. Component same minor version as api-server
   1.19.2
   1 -> Major
   19 -> Minor
   2 -> Patch

- kubectl not affected by version, could be install on anyversion master
- Components same minor version as apiserver or one below

#### How to

1. kubectl drain
   - Safely evict all pods from node
   - Mark node as SchedulingDisabled (kubectl cordon)

#### How to make application survive on upgrade

- Pod gracePeriod / Terminating state
- Pod Lifecycle Events
- PodDisruptionBudget

#### Upgrading Control Plane

`ssh controlplane`
`k get nodes`
`k drain control-plane`
`k drain control-plane --ignore-daemonsets`
`k get node`
`k get nodes` //control-plane scheduling disabled
`apt-get pdate`
`apt-cache show kubeadm | grep 1.xx` // version
`apt-mark hold kubectl kubelet` // disable update on kubectl kubelet
`apt-mark unhold kubeadm`
`apt-get install kubeadm=1.xx.x-00`
`kubeadm version`
`kubeadm upgrade plan` // check possible update
`kubeadm upgrade apply v1.xx.x-00`

#### Upgrading Kubectl & Kubectl

`kubectl version`
`apt-mark unhold kubelet kubectl`
`apt-get install kubelet-1.xx.xx-00 kubectl-1.xx.x-00` //same version with kubeadm
`service restart kubelet`
`kubeadm upgrade plan` //verify there is nothing to update
`k get nodes`

`k uncordon control-plane` //enable sceduling control-plane

#### Upgrading Node

`ssh controlplane`
`k drain node`
`k drain node --ignore-daemonsets`

`ssh node`
`apt-get update`
`apt-cache show kubeadm | grep 1.xx`
`apt-mark hold kubelet kubectl` //hold kubelet and kubectl
`apt-get install kubeadm=1.xx.x-00`
`kubeadm upgrade node`
`apt-mark hold kubeadm`
`apt-mark unhold kubelet kubectl`
`apt-get install kubelet=1.xx.x-00 kubectl=1.xx.x-00`
`apt-mark hold kubectl kubelet`
`service restart kubelet`

`ssh controlplane`
`k get nodes --version`
`k uncordon node`
`k get nodes`
