
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

## Perform an etcd backup and restore.
```sh
ETCDCTL_API=3 etcdctl --endpoints 10.2.0.9:2379 \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
snapshot save snapshot.db

kubectl run pod-test --image nginx

cp /etc/kubernetes/manifest/* ~/backup/

mv /etc/kubernetes/manifest/* ..

ETCDCTL_API=3 etcdctl --endpoints 10.2.0.9:2379 \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
--data-dir /var/backup/etcd snapshot restore snapshot.db

vim ~/backup/etcd.yaml

cp ~/backup/* /etc/kubernetes/manifest/

kubectl get po
```

    