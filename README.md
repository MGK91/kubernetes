# Kubernetes Architecture and Concepts

## Architecture

### Control Plane Components

* **etcd**: Key-value store used as Kubernetes' backing store for all cluster data.
* **API Server** (`kube-apiserver`): Serves the Kubernetes API, the front-end for the control plane.
* **Controller Manager** (`kube-controller-manager`): Runs controller processes such as:

  * Node Controller
  * Replication Controller
  * Job Controller
  * EndpointSlice Controller
* **Scheduler** (`kube-scheduler`): Assigns pods to nodes based on resource availability.
* **Cloud Controller Manager**: Integrates with cloud providers for managing resources like load balancers, nodes, etc.

### Worker Node (Data Plane)

* **Kubelet**: Ensures containers are running in a Pod.
* **Kube-proxy**: Maintains network rules and handles communication inside/outside the cluster.

### Architecture Diagram

![Kubernetes Architecture Diagram](https://kubernetes.io/images/docs/components-of-kubernetes.svg)

---

## Pods

* Smallest deployable unit in Kubernetes.
* Can contain one or multiple containers.
* Ephemeral in nature.
* Use Linux namespaces and cgroups for isolation.

### Sample Pod YAML

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - name: nginx
      image: nginx:1.14.2
      ports:
        - containerPort: 80
```

Apply with: `kubectl apply -f pod.yaml`

## Workload Resources

### Deployment

* Manages stateless applications.
* Supports rollouts, rollbacks, and scaling.

### StatefulSet

* Used for stateful applications requiring persistent storage and stable identities.

### DaemonSet

* Ensures all nodes run a copy of a pod.
* Useful for running node-level agents like log collectors.

### Pod Lifecycle

* Starts with `Pending`, moves to `Running`, and finally `Succeeded` or `Failed`.
* Exit codes determine status.
* Pods are marked for deletion when nodes become unavailable.

## Container Probes and Types

### Probes

* **Liveness Probe**: Determines when to restart a container.
* **Readiness Probe**: Determines when a container is ready to serve traffic.

### Container Types

* **Init Container**: Runs before main container and must complete successfully.
* **Sidecar Container**: Runs alongside the main container, useful for logging or proxies.
* **Ephemeral Containers**: Used for debugging purposes only.

## Workload Examples

### Deployment Example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.14.2
          ports:
            - containerPort: 80
```

### ReplicaSet Example

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: front
  labels:
    app: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.14.2
          ports:
            - containerPort: 80
```

## Other Workload Types

* **Job**: One-off task that completes and exits.
* **Replication Controller**: Legacy method for managing replicas. Superseded by ReplicaSet and Deployment.

## Autoscaling

* **Horizontal Pod Autoscaler (HPA)**: Scales pods based on CPU/memory.
* **Vertical Pod Autoscaler (VPA)**: Adjusts resource requests/limits for pods.
* **Event-Driven Autoscaling**: Not native; implemented via KEDA.

## Services

* **ClusterIP**: Internal-only access.
* **NodePort**: Access via node IP and port.
* **LoadBalancer**: External access through a cloud provider's load balancer.

## Ingress

* Exposes HTTP/HTTPS routes from outside the cluster to services.
* Routes traffic based on rules defined in the Ingress resource.

---

> This document provides a concise overview of Kubernetes architecture and fundamental concepts including workloads, autoscaling, and networking.
