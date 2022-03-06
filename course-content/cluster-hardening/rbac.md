### Practice Role and Rolebinding

`kubectl create ns red`
`kubectl create ns blue`

`kubectl create role secret-manager --verb=get --resource=secrets -n red -oyaml --dry-run=client`
`kubectl create rolebinding secret-manager --role=secret-manager --user=jane -n red`
`kubectl create role secret-manager --ver=get --verb=list --resource=secrets -n blue -oyaml --dry-run=client`
`kubectl create rolebinding secret-manager --role=secret-manger --user=jane -n blue`

`kubectl auth can-i get secret -n red --as jane`
`kubectl auth can-i get po -n red --as jane`
`kubectl auth can-i get secret -n blue --as jane`

`kubectl auth can-i get secret -n blue --as jane`
`kubectl auth can-i list secrets -n blue --as jane`
`kubectl auth can-i get po -n blue --as jane`
`kubectl auth can-i get secret -n red --as jane`

### Practice Cluster Role Binding

`kubectl create clusterrole deploy-deleter --verb=delete --resource=deployment -oyaml --dry-run=client`
`kubectl create clusterrolebinding --clusterrole=deploy-deleter --verb=delete --user=jane`

`kubectl create clusterrole deploy-deleter --verb=delete --resource-deployment -n red -oyaml --dry-run=client`
`kubectl create clusterrolebinding --clusterrole=deploy-deleter --verb=delete -n red --user=jim`

`kubectl auth can-i delete deployment -A --as jane`
`kubectl auth can-i delete pod -A --as jane`

`kubectl auth can-i delete deployment -n red --as jim`
`kubectl auth can-i delete deployment -A --as jim`

### Create CSR (Certificate Signing Request)

`openssl genrsa -out jim.key 2048`
`openssl req -new -key jim.key -out jim.csr` <!-- Common Name Jim -->

`cat jim.csr | base64 -w 0` <!-- Copy base64 encode -->

#### csr-jim.yaml

```
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: jim
spec:
  groups:
  - system:authenticated
  request: **paste-here**
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
```

`kubectl apply -f csr-jim.yaml`

`kubectl get csr <!-- Status pending -->`

`kubectl certitificate approve jim <!-- Status approved -->`

`kubectl get csr jim -oyaml <!-- copy certficate code base64 -->`

`echo "base64 code" | base64 -d > jim.crt` <!-- Autentication crt using cert -->

`kubectl config view`
`kubectl config set-credentials jim --client-key=jim.key --client-certificate=jim.crt`
`kubectl config set-credentials jim --client-key=jim.key --client-certificate=jim.crt --embed-certs`
`kubectl config get-contexts`
`kubectl config set-context jim --user=jim --cluster=kubernetes`
`kubectl config use-context jim`
`kubectl config get-contexts`
