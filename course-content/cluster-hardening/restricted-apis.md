### Cluster Hardening - Restricted APIs

#### Anonymous Acccess

`vim /etc/kubernetes/manifests/kube-apiserver.yaml`
Add line
`- --anonymous-auth=false`

#### Insecure Acccess

v1.6 <
`vim /etc/kubernetes/manifests/kube-apiserver.yaml`
Add line
`- --insecure-port=8080`

#### Manual API Request

`k config view --raw`
copy token certificate-authority-data
`echo "token-ca-authorityata" | base64 -d > ca`

copy token client-certificate-data
`echo "token-client-cert-data" | base64 -d > key`

copy token client-key-data
`echo "token-client-key-data" | base64 -d > cert`

`curl http://ipaddr:6443 --cacert ca --cert crt --key key`

#### External APIServer Access

`curl http://ipeksternal:31710`

<!-- Do in local mahine -->

`k config view --raw` paste to local conf
change server to `https://ipeksternal:nodeport`

<!-- Do in kubemachine -->

`cd /etc/kubernetes/pki` (private key infrastructure)
`openssl x509 -in apiserver.cert -text`

<!-- do in local machine -->

`echo "ipeksternal kubernetes" >> /etc/hosts`
`vi conf`
change ipeksternal to `kubernetes:nodeport`

`k --kubeconfig conf get ns`

### Logs Kube Location

<!-- Reading logs file -->

`cat /var/log/pods/kube-system_kube`
`cat /var/log/pods/kube-system_kube-apiserver-xx`

<!-- Using critctl  -->

`crictl ps`
`crictl logs containerid`

<!-- Syslog -->

`journalctl | grep apiserver`
`cat /var/log/syslog | grep apiserver`
