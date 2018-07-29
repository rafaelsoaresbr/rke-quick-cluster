# rke-quick-cluster
Starting up a single node cluster using RKE

## What you will need:

0. A linux machine running openssh-server with an authorized user key and docker configured to [Manage Docker as a non-root user](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user)

## How to:

1. Download the RKE binary from https://github.com/rancher/rke/releases/latest
```
$ curl -LO https://github.com/rancher/rke/releases/download/v0.1.8/rke_linux-amd64
```
```
$ chmod +x rke_linux-amd64
```
2. Install kubectl binary via curl
```
$ curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
```
```
$ chmod +x ./kubectl
```
* Optional step:
```
$ mv ./kubectl ~/bin/kubectl
```
3. Edit cluster.yml and change the address, user and ssh_key_path

4. Deploying Kubernetes with RKE
```
$ ./rke_linux-amd64 up
```
5. Interacting with your Kubernetes cluster
```
$ ./kubectl --kubeconfig=kube_config_cluster.yml version
```
6. Deploy Dashboard, execute following command:
```
$ ./kubectl --kubeconfig=kube_config_cluster.yml apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml
```
7. Create Service Account
```
$ ./kubectl --kubeconfig=kube_config_cluster.yml apply -f dashboard/service-account.yaml
```
8. Create ClusterRoleBinding
```
$ ./kubectl --kubeconfig=kube_config_cluster.yml apply -f dashboard/cluster-role-binding.yaml
```
9. Bearer Token (namespace-controller-token)
```
$ ./kubectl --kubeconfig=kube_config_cluster.yml -n kube-system describe secret $(./kubectl --kubeconfig=kube_config_cluster.yml -n kube-system get secret | grep admin-user | awk '{print $1}')
```
10. create a secure channel to your Kubernetes cluster:
```
$ ./kubectl --kubeconfig=kube_config_cluster.yml proxy
```
11. Now access Dashboard at:

http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
