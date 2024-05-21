
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

    
## Update a pod by adding a sidecar container with busybox for logs, and add an emptyDir volume.
```sh
kubectl get po legacy-app -o yaml > legacy-app.yaml
kubectl delete po legacy-app

vim legacy-app.yaml
```

### Deployment Definition
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  labels:
    app: myapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
        - name: myapp
          image: alpine:latest
          command: ['sh', '-c', 'while true; do echo "logging" >> /var/log/logs.txt; sleep 1; done']
          volumeMounts:
            - name: data
              mountPath: /var/log
        - name: logshipper
          image: alpine:latest
          command: ['sh', '-c', 'tail -F /var/log/logs.txt']
          volumeMounts:
            - name: data
              mountPath: /var/log
      volumes:
        - name: data
          emptyDir: {}
```

```sh
kubectl apply -f legacy-app.yaml

# Verify the pod and containers are running and the volume exists
kubectl get po 
kubectl exec -it myapp-12345 -c myapp -- sh -c "ls /var/log && cat /var/log/logs.txt"
```
