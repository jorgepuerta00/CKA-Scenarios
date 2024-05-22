
# Storage

## Create a PVC named pv-volume with 10Mi and a Pod using nginx:alpine that uses the PVC. Additionally, update the PVC to increase its capacity to 70Mi.

### PersistentVolume Definition
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-volume
spec:
  storageClassName: storage-class-name
  capacity:
    storage: 10Mi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /mnt/data
```

### PersistentVolumeClaim Definition
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-volume
spec:
  storageClassName: storage-class-name
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Mi
```

### Pod Definition
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-name
spec:
  volumes:
    - name: task-pv-storage
      persistentVolumeClaim:
        claimName: pvc-volume
  containers:
    - name: task-pv-container
      image: nginx
      volumeMounts:
        - mountPath: /usr/share/nginx/html
          name: task-pv-storage
```

### Verify
```sh
kubectl get pv,pvc

kubectl exec -it pod-name -- ls /usr/share/nginx/html
```

[Configure a Pod to Use a PersistentVolume for Storage | Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/)  

## Create a standalone PersistentVolume (PV).
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: task-pv-volume
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
```

[Persistent Volumes | Kubernetes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)  