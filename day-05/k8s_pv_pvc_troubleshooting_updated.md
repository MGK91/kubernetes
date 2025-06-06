
# Kubernetes Persistent Volumes (PV) and Persistent Volume Claims (PVC)

## 1. Persistent Volumes (PV)
A **Persistent Volume (PV)** in Kubernetes is a piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using a StorageClass. It exists beyond the lifecycle of individual Pods and is used to store data that should persist even after Pods are deleted or rescheduled.

### Key Points:
- PVs are associated with a specific storage resource, such as AWS EBS, NFS, or GCE Persistent Disks.
- PVs have a lifecycle independent of Pods and can be reused by different Pods.
- PVs are created using YAML files or automatically created via StorageClass.

### Example of PV using AWS EBS GP2 (without volumeID):
In a dynamic provisioning setup, the `volumeID` is not needed as Kubernetes will handle the creation and attachment of the EBS volume based on the StorageClass.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-ebs-pv
spec:
  capacity:
    storage: 10Gi
  volumeMode: Filesystem
  persistentVolumeReclaimPolicy: Retain
  storageClassName: gp2
  accessModes:
    - ReadWriteOnce
  awsElasticBlockStore:
    fsType: ext4
```

### Explanation:
- The `awsElasticBlockStore` is defined, but no `volumeID` is specified. Kubernetes will dynamically provision the EBS volume when the PVC is created and bound to this PV.

---

## 2. Persistent Volume Claims (PVC)
A **Persistent Volume Claim (PVC)** is a request for storage by a user. It allows users to request specific amounts of storage from the available PVs. PVCs abstract the underlying storage provider and can be bound to a PV that matches the request.

### Key Points:
- A PVC specifies the amount of storage required and the access mode (e.g., ReadWriteOnce).
- PVCs can either be dynamically or statically bound to PVs.

### Example of PVC:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: gp2
```

### Explanation:
- The PVC requests 10Gi of storage with `ReadWriteOnce` access mode, which means it can only be mounted by a single node at a time.
- The `storageClassName: gp2` ensures that the PVC is provisioned using the AWS GP2 storage class.

---

## 3. Sample Deployment Manifest Using PVC
Here is an example of a **Deployment** manifest that uses the PVC created above to mount the persistent volume into the Pod.

### Example of Deployment:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: nginx:latest
        ports:
        - containerPort: 80
        volumeMounts:
        - mountPath: /usr/share/nginx/html
          name: my-pvc-volume
      volumes:
      - name: my-pvc-volume
        persistentVolumeClaim:
          claimName: my-pvc
```

### Explanation:
- This Deployment creates a single replica Pod running the NGINX container.
- The container mounts the PVC (`my-pvc`) to the path `/usr/share/nginx/html` inside the container.
- The volume is linked to the PVC created earlier, providing persistent storage for the Pod.

---

## 4. Troubleshooting PV and PVC with AWS GP2
When working with **Persistent Volumes (PV)** and **Persistent Volume Claims (PVC)** in Kubernetes with AWS GP2 (EBS), you may run into several issues. Here are common troubleshooting steps:

### Common Issues:
- **PVC is stuck in Pending state**: This often occurs when there is no available PV that matches the request.
    - **Solution**: Check the status of the PVs in your cluster to ensure there are PVs available that match the `storageClassName`, `accessModes`, and `storage` requested by the PVC.
    ```bash
    kubectl get pv
    kubectl describe pvc my-pvc
    ```

- **Insufficient Storage**: The PVC requests more storage than is available in the underlying PV or the underlying AWS EBS volume.
    - **Solution**: Ensure that the requested storage size for the PVC is within the limits of the available PV. If necessary, resize the EBS volume in AWS or create a new larger volume and update the PV.

- **AccessMode Mismatch**: The PVC might request `ReadWriteOnce`, but the available PV might not support this access mode, or the PV might not be mounted properly.
    - **Solution**: Ensure that the `accessModes` for the PV and PVC match. For example, AWS EBS volumes support `ReadWriteOnce`, meaning that the volume can only be mounted by a single node at a time.

- **Volume not Detach from Node**: Sometimes, the EBS volume does not detach from the node after a Pod is deleted, preventing it from being reused by another Pod.
    - **Solution**: Check the EBS volume status in AWS and manually detach the volume if necessary. You can also check for issues with the Kubernetes node in the cloud provider's console.

### Logs and Status Checks:
- You can check the logs of the controller responsible for binding the PV and PVC by running:
    ```bash
    kubectl logs -n kube-system <controller-pod-name>
    ```

### General Commands for Troubleshooting:
- Check the status of the PVC and PV:
    ```bash
    kubectl get pvc
    kubectl get pv
    kubectl describe pvc <pvc-name>
    kubectl describe pv <pv-name>
    ```

---

### Conclusion
PVs and PVCs provide a way to manage persistent storage in Kubernetes. By using the right `StorageClass` and troubleshooting common issues, you can effectively manage your storage in cloud environments like AWS with EBS GP2 volumes.
