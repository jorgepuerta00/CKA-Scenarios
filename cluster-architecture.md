
# Cluster Architecture, Installation & Configuration

## Upgrade the master node to version 1.29.1
```sh
kubectl drain <node-to-drain> --ignore-daemonsets

sudo apt update
sudo apt-cache madison kubeadm

sudo apt-mark unhold kubeadm && \
sudo apt-get update && sudo apt-get install -y kubeadm='1.29.1-*' && \
sudo apt-mark hold kubeadm

kubeadm version
sudo kubeadm upgrade plan

sudo kubeadm upgrade apply v1.29.1

sudo apt-mark unhold kubelet kubectl && \
sudo apt-get update && sudo apt-get install -y kubelet='1.29.1-*' kubectl='1.29.1-*' && \
sudo apt-mark hold kubelet kubectl

sudo systemctl daemon-reload
sudo systemctl restart kubelet

kubectl uncordon <node-to-uncordon>
```

[Upgrading kubeadm clusters | Kubernetes](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/)  

## Perform an etcd backup and restore.
```sh
# create the snapshot
ETCDCTL_API=3 etcdctl --endpoints 10.2.0.9:2379 \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
snapshot save snapshot.db

# verify using a pod, when restore the backup then the pod shouldn't be there
k run pod-test --image nginx

# backup static pods
cp /etc/kubernetes/manifest/* ~/backup/

# remove all static pods
mv /etc/kubernetes/manifest/* ..

# restore the snapshot
ETCDCTL_API=3 etcdctl --endpoints 10.2.0.9:2379 \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
--data-dir /var/backup/etcd snapshot restore snapshot.db

# modify the etc.yaml replace the old data-dir /var/lib/etcd by the new one /var/backup/etcd
	vim ~/backup/etcd.yaml

# restore static pods
cp ~/backup/* /etc/kubernetes/manifest/

# verify that the pod-test is not longer exist
k get po
```

[Operating etcd clusters for Kubernetes | Kubernetes](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/)  
    
## Create a clusterrole, service account, and then a rolebinding for app-team1. The clusterrole should only allow creating deployments, statefulsets, and daemonsets.
```sh
kubectl create sa sa-name -n app-team1
kubectl create clusterrole cr-name -n app-team1 --verb create --resources deploy,sts,ds
kubectl create clusterrolebinding crb-name -n app-team1 --clusterrole cr-name --serviceaccount app-team1:sa-name
```

### Verify
```sh
kubectl auth can-i list pods --as=system:serviceaccount:app-team1:sa-name -n app-team1
No

kubectl auth can-i create pods --as=system:serviceaccount:app-team1:sa-name -n app-team1
No

kubectl auth can-i create ds --as=system:serviceaccount:app-team1:sa-name -n app-team1
Yes

kubectl auth can-i create deploy --as=system:serviceaccount:app-team1:sa-name -n app-team1
Yes
```

[kubectl create clusterrole | Kubernetes](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_create/kubectl_create_clusterrole/)  
[kubectl auth can-i | Kubernetes](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_auth/kubectl_auth_can-i/)  