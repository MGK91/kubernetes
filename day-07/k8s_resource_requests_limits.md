
# Kubernetes Resource Requests and Limits

## 1. Introduction to Resource Requests and Limits

In Kubernetes, **resource requests** and **resource limits** are used to manage the CPU and memory usage of Pods. These settings help Kubernetes allocate the right amount of resources to Pods, ensuring that applications run smoothly without overusing or underusing resources.

- **Resource Requests**: Defines the minimum amount of CPU or memory a container needs. Kubernetes uses this value to schedule the Pod on a node with enough available resources.
- **Resource Limits**: Defines the maximum amount of CPU or memory a container can use. Kubernetes enforces this limit to prevent a container from using more than its fair share of resources.

### Key Concepts:
- **CPU Requests**: The amount of CPU resources that Kubernetes will guarantee for the container. This is used for scheduling.
- **CPU Limits**: The maximum amount of CPU the container can consume.
- **Memory Requests**: The amount of memory that Kubernetes will guarantee for the container.
- **Memory Limits**: The maximum amount of memory the container can consume.

---

## 2. Setting Resource Requests and Limits

### Example 1: Defining Resource Requests and Limits in a Deployment

In the following example, a Deployment is defined with resource requests and limits for both CPU and memory.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 2
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
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
```

### Explanation:
- **requests**:
  - `memory: "64Mi"`: Requests 64MB of memory for the container.
  - `cpu: "250m"`: Requests 250 milliCPU (0.25 CPU core).
- **limits**:
  - `memory: "128Mi"`: Limits the container's memory usage to 128MB.
  - `cpu: "500m"`: Limits the container's CPU usage to 500 milliCPU (0.5 CPU core).

### Use Case for Resource Requests and Limits:
- **Ensuring fair resource allocation**: In a shared Kubernetes environment, you want to ensure that no single application consumes more than its fair share of resources, which could affect other applications running in the same cluster.

---

## 3. Resource Requests and Limits in Practice

### Example 2: Using Requests and Limits with Multiple Containers

Here’s an example where multiple containers are deployed within a single Pod, and resource requests and limits are set for each container.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: multi-container-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: multi-container-app
  template:
    metadata:
      labels:
        app: multi-container-app
    spec:
      containers:
      - name: web-container
        image: nginx:latest
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
      - name: api-container
        image: my-api-image:latest
        resources:
          requests:
            memory: "128Mi"
            cpu: "500m"
          limits:
            memory: "256Mi"
            cpu: "1"
```

### Explanation:
- The **web-container** and **api-container** are both part of the same Pod, and their resources are allocated independently. The `web-container` is allocated less memory and CPU compared to the `api-container`, which is expected to handle more intensive work.

### Use Case for Multiple Containers in a Pod:
- **Microservices with shared resources**: A Pod containing multiple containers that interact with each other (e.g., a web frontend and an API backend) and require distinct resource allocations.

---

## 4. Resource Management Strategies

### Example 3: Setting Up Resource Requests and Limits for Stateful Applications

For stateful applications like databases (e.g., MongoDB, Cassandra), it’s important to set higher memory requests to ensure that the application has sufficient resources to handle data operations.

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: my-database
spec:
  serviceName: "my-database-service"
  replicas: 1
  selector:
    matchLabels:
      app: my-database
  template:
    metadata:
      labels:
        app: my-database
    spec:
      containers:
      - name: db-container
        image: mongo:latest
        resources:
          requests:
            memory: "2Gi"
            cpu: "1"
          limits:
            memory: "4Gi"
            cpu: "2"
```

### Explanation:
- **requests**: MongoDB is allocated 2GB of memory and 1 CPU core for normal operations.
- **limits**: The memory and CPU usage are capped at 4GB and 2 CPU cores, respectively.

### Use Case for Stateful Applications:
- **Ensuring adequate resources for stateful apps**: Databases or services handling critical, stateful data need to have consistent and guaranteed resources to prevent performance issues, especially during high load.

---

## 5. Best Practices for Resource Requests and Limits

### 5.1. Monitor and Adjust Requests and Limits Based on Metrics
Kubernetes does not automatically adjust resource requests and limits based on usage, so it's important to monitor and adjust them based on actual application performance. Tools like **Prometheus** and **Grafana** can help monitor resource usage, and tools like **Vertical Pod Autoscaler (VPA)** can automatically adjust resource requests and limits.

### 5.2. Set Proper Resource Requests for Efficient Scheduling
Setting appropriate **requests** helps Kubernetes schedule Pods effectively. If requests are too high, Pods will be scheduled on underutilized nodes, wasting resources. If requests are too low, Pods may be scheduled on nodes that cannot provide enough resources.

### 5.3. Set Limits to Prevent Resource Starvation
Set **limits** to prevent Pods from consuming excessive resources, which could cause other Pods on the same node to be evicted or throttled.

---

## 6. Conclusion

By setting **resource requests** and **limits**, you can ensure that your applications run efficiently in a Kubernetes environment. Requests ensure Pods have the necessary resources for operation, while limits protect the cluster from resource hogging and ensure fair allocation. Always monitor your applications and adjust these values to ensure the best performance and reliability of your workloads.
