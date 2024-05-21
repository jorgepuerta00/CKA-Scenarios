
# Troubleshooting

## Obtain logs from the pod bar, grep for "error," and save the result to a file.
```sh
kubectl logs bar | grep error > result-file.txt
cat result-file.txt
```

## Troubleshoot "unready" nodes
```sh
ssh node01
systemctl restart kubelet
systemctl status kubelet
```

## Add a NoExecute taint to a node to evict all pods and ensure they are rescheduled on other nodes.
```sh
kubectl taint nodes node01 maintenance=true:NoExecute

ssh node01
ls /etc/kubernetes/manifests/
sudo mv /etc/kubernetes/manifests/kube-proxy.yaml /etc/kubernetes/manifests/kube-proxy.yaml.bak

kubectl drain node01 --ignore-daemonsets --delete-emptydir-data
kubectl delete pod -n kube-system kube-proxy
```

    