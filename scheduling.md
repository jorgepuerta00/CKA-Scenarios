
# Workloads & Scheduling

## Create a pod with two containers.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: podName
spec:
  containers:
  - name: container1
    image: image1
  - name: container2
    image: image2
```

## Count the nodes without the NoSchedule taint in a 3-node cluster.
```sh
kubectl get nodes -ojson | jq '.items[].spec.taints'
```
Output:
```json
[
   { key: NoSchedule ...},
   null,
   null
]
```
```sh
echo '2' > result-file.txt
```

## Scale a deployment named deployment from 2 replicas to 6.
```sh
kubectl get deploy 
kubectl scale deploy/deployment --replicas 6

# verify
kubectl get deploy 
```

## List of pods sorted by CPU usage and filter by label
```sh
kubectl top pods --sort-by cpu --label cpu=loader
echo 'pod3 pod1 pod2' > result.txt

# modify the file to do a list of pods
vim result.txt
pod3
pod1
pod2
```

    