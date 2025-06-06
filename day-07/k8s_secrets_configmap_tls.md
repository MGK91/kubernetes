
# Kubernetes Secrets, ConfigMaps, and TLS Secrets with NGINX for TLS Offloading

## 1. Kubernetes Secrets

Kubernetes **Secrets** are used to store sensitive information such as passwords, OAuth tokens, SSH keys, etc. Secrets are encoded in base64 format and can be mounted as volumes or used as environment variables in Pods.

### Example 1: Creating a Secret from Literal Values

```bash
kubectl create secret generic my-secret --from-literal=username=admin --from-literal=password=mysecretpassword
```

### Explanation:
- `--from-literal`: Specifies the key-value pairs directly from the command line.
- The Secret is stored in the cluster, and its data can be accessed by Pods in the namespace.

### Example 2: Creating a Secret from a File

```bash
kubectl create secret generic my-secret --from-file=ssh-privatekey=/path/to/private.key
```

### Explanation:
- The Secret is created from a file, such as an SSH private key.

### Use Case for Secrets:
- Storing database credentials that should not be exposed in a configuration file.
- Providing sensitive API keys to applications.

---

## 2. Kubernetes ConfigMaps

**ConfigMaps** are used to store non-sensitive configuration data in the form of key-value pairs. They allow you to separate configuration from your application code.

### Example 1: Creating a ConfigMap from Literal Values

```bash
kubectl create configmap my-config --from-literal=appMode=production --from-literal=logLevel=debug
```

### Explanation:
- `--from-literal`: Specifies the key-value pairs directly from the command line.

### Example 2: Creating a ConfigMap from a File

```bash
kubectl create configmap my-config --from-file=config-file=/path/to/config-file.txt
```

### Explanation:
- The ConfigMap is created from a file containing configuration data.

### Example 3: Using ConfigMap in a Deployment

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
        envFrom:
        - configMapRef:
            name: my-config
```

### Explanation:
- The `envFrom` section loads environment variables from the `my-config` ConfigMap into the container.

### Use Case for ConfigMaps:
- Storing application configuration settings like API endpoints, feature flags, or other environment-specific variables.

---

## 3. TLS Secret Example for NGINX TLS Offloading

In a production environment, securing traffic with **TLS** (Transport Layer Security) is crucial. Kubernetes allows you to use TLS secrets for SSL termination and encryption.

### Example 1: Creating a TLS Secret

To create a TLS secret, you need to have a certificate file (`tls.crt`) and a private key file (`tls.key`).

```bash
kubectl create secret tls my-tls-secret --cert=/path/to/tls.crt --key=/path/to/tls.key
```

### Explanation:
- `my-tls-secret`: The name of the secret.
- `--cert`: The path to the TLS certificate.
- `--key`: The path to the TLS private key.

### Example 2: Using the TLS Secret in an Ingress Resource for TLS Offloading

Here’s an example of how to use the `my-tls-secret` in an Ingress resource to handle TLS offloading with NGINX.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-tls-ingress
  namespace: default
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  rules:
  - host: my-app.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-service
            port:
              number: 80
  tls:
  - hosts:
    - my-app.example.com
    secretName: my-tls-secret
```

### Explanation:
- `tls`: The TLS section in the Ingress resource specifies the use of the `my-tls-secret` for SSL termination.
- `nginx.ingress.kubernetes.io/ssl-redirect`: This annotation ensures that HTTP traffic is automatically redirected to HTTPS.

### Use Case for TLS Secret with NGINX Ingress:
- Offloading TLS termination to NGINX in a Kubernetes cluster, ensuring that all incoming traffic to the application is encrypted and secure.
- The Ingress resource routes traffic over HTTPS and handles SSL/TLS certificates for your domain.

---

## 4. Combining Secrets and ConfigMaps in a Production Deployment

In production, you might need to use both **Secrets** and **ConfigMaps** for managing configuration and sensitive data. Here’s an example of a deployment using both.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
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
        image: my-app-image:latest
        env:
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-db-secret
              key: password
        - name: APP_MODE
          valueFrom:
            configMapKeyRef:
              name: my-config
              key: appMode
```

### Explanation:
- The deployment uses both `Secret` and `ConfigMap`:
  - `DB_PASSWORD`: Retrieved from a Secret.
  - `APP_MODE`: Retrieved from a ConfigMap.

### Use Case for Combined Secrets and ConfigMaps:
- **Secrets** are used for sensitive data like database credentials, while **ConfigMaps** are used for non-sensitive configuration like application mode or log level.

---

### Conclusion

Kubernetes **Secrets** and **ConfigMaps** provide a secure and flexible way to manage sensitive and non-sensitive data, respectively. **TLS secrets** are especially useful for offloading SSL/TLS termination at the ingress level. By using these resources effectively, you can build secure and manageable production-grade applications.
