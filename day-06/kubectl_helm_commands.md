
# Kubernetes `kubectl` and Helm Commands with Examples and Use Cases

## 1. `kubectl` Commands

`kubectl` is the command-line tool for interacting with Kubernetes clusters. Below are some commonly used `kubectl` commands with examples and use cases.

### 1.1. Get Cluster Information

To view the cluster's current context, configuration, and nodes.

```bash
kubectl cluster-info
```

**Use Case**:
- Verifying the cluster status and the URLs of your Kubernetes master and services.

---

### 1.2. Get Pods

To list all Pods in a specific namespace or across the entire cluster.

```bash
kubectl get pods -n default
```

**Use Case**:
- Quickly checking the status of all running Pods in a namespace.

---

### 1.3. Describe a Pod

To view detailed information about a specific Pod, including events and status.

```bash
kubectl describe pod <pod-name> -n default
```

**Use Case**:
- Debugging issues with a Pod, such as checking logs, events, or resource usage.

---

### 1.4. Get Services

To list all the services in a specific namespace.

```bash
kubectl get services -n default
```

**Use Case**:
- Checking the network services and their endpoints in a namespace.

---

### 1.5. Deploy an Application

To create a Deployment with a specific image.

```bash
kubectl create deployment my-app --image=nginx:latest
```

**Use Case**:
- Quickly deploying an application using a specific container image.

---

### 1.6. Expose a Pod/Service

To expose a Pod or Service as an external access point.

```bash
kubectl expose deployment my-app --type=LoadBalancer --port=80 --target-port=80
```

**Use Case**:
- Exposing an application to external traffic using a LoadBalancer service.

---

### 1.7. Scaling Deployments

To scale the number of replicas in a deployment.

```bash
kubectl scale deployment my-app --replicas=5
```

**Use Case**:
- Dynamically adjusting the number of replicas based on the load.

---

### 1.8. Apply Configuration from a YAML File

To apply a configuration from a file (like Deployment, Service, etc.).

```bash
kubectl apply -f deployment.yaml
```

**Use Case**:
- Deploying or updating resources in a Kubernetes cluster from a YAML file.

---

### 1.9. Delete a Pod

To delete a specific Pod.

```bash
kubectl delete pod <pod-name> -n default
```

**Use Case**:
- Deleting a problematic or unnecessary Pod to free up resources.

---

### 1.10. Get Logs of a Pod

To view the logs of a specific Pod.

```bash
kubectl logs <pod-name> -n default
```

**Use Case**:
- Checking the logs of a Pod to troubleshoot or monitor application behavior.

---

## 2. Helm Commands

**Helm** is a package manager for Kubernetes that simplifies the deployment and management of applications.

### 2.1. Install a Helm Chart

To install a Helm chart (e.g., installing NGINX ingress controller).

```bash
helm install my-nginx ingress-nginx/ingress-nginx
```

**Use Case**:
- Installing and managing Kubernetes applications (like NGINX, MySQL, etc.) using Helm charts.

---

### 2.2. List Installed Releases

To list all Helm releases in a specific namespace.

```bash
helm list -n default
```

**Use Case**:
- Viewing the list of applications that have been deployed with Helm in a particular namespace.

---

### 2.3. Upgrade a Release

To upgrade an existing Helm release to a new version or configuration.

```bash
helm upgrade my-nginx ingress-nginx/ingress-nginx --set controller.replicaCount=3
```

**Use Case**:
- Updating an existing application to a new version or changing configuration values.

---

### 2.4. Uninstall a Helm Release

To remove a Helm release and all of its associated resources.

```bash
helm uninstall my-nginx -n default
```

**Use Case**:
- Uninstalling applications or services deployed via Helm.

---

### 2.5. Get Information About a Helm Release

To view detailed information about a specific Helm release.

```bash
helm status my-nginx -n default
```

**Use Case**:
- Getting the current status, revision, and values of a Helm release.

---

### 2.6. Install a Helm Chart with Custom Values

To install a Helm chart with custom values.

```bash
helm install my-release nginx-ingress/nginx-ingress --set controller.replicaCount=2
```

**Use Case**:
- Customizing the configuration values of a Helm chart at installation time (e.g., changing replica count, setting resource limits).

---

### 2.7. Rollback a Helm Release

To rollback a Helm release to a previous version.

```bash
helm rollback my-nginx 1 -n default
```

**Use Case**:
- Rolling back to a previous stable version of an application if a deployment fails or introduces issues.

---

### 2.8. Create a Helm Chart

To create a new Helm chart for packaging your own application.

```bash
helm create my-chart
```

**Use Case**:
- Creating your own Helm charts to deploy custom applications or services in Kubernetes.

---

### 2.9. Package a Helm Chart

To package a Helm chart into a `.tgz` archive.

```bash
helm package my-chart/
```

**Use Case**:
- Creating a packaged Helm chart for distribution or deployment.

---

### 2.10. Fetch a Helm Chart

To download a Helm chart to your local machine.

```bash
helm fetch ingress-nginx/ingress-nginx
```

**Use Case**:
- Downloading and inspecting a Helm chart locally before deploying it in a cluster.

---

## Conclusion

These are just a few of the essential `kubectl` and `helm` commands that are frequently used for managing Kubernetes clusters and applications. Mastering these commands will help you efficiently manage resources, deploy applications, and troubleshoot issues in your Kubernetes environment.
