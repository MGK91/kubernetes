
# NGINX Ingress Controller Installation and Ingress Resources in Kubernetes

## 1. Installing NGINX Ingress Controller using Helm

NGINX Ingress Controller is a Kubernetes Ingress controller that uses NGINX to route external HTTP and HTTPS traffic to services within your Kubernetes cluster.

### Step 1: Add the NGINX Ingress Helm Chart

First, add the NGINX Ingress controller Helm chart repository.

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
```

### Step 2: Install the NGINX Ingress Controller

You can install the NGINX Ingress Controller with the following command. This will install it in the `ingress-nginx` namespace.

```bash
helm install my-ingress-nginx ingress-nginx/ingress-nginx --namespace ingress-nginx --create-namespace
```

- `my-ingress-nginx`: The name of the release.
- `--namespace ingress-nginx`: Specifies the namespace where the controller will be installed.
- `--create-namespace`: Creates the namespace if it doesn't exist.

### Step 3: Verify the Installation

To verify the NGINX Ingress Controller is running, use the following command:

```bash
kubectl get pods -n ingress-nginx
```

You should see a pod running with the name starting with `my-ingress-nginx`.

### Step 4: Expose the NGINX Ingress Controller

To expose the NGINX Ingress Controller to external traffic, create a Service of type `LoadBalancer`.

```bash
kubectl expose deployment my-ingress-nginx-controller --namespace ingress-nginx --type=LoadBalancer --name=ingress-nginx
```

This will create a LoadBalancer service that provides an external IP for your Ingress Controller.

---

## 2. Creating Ingress Resources

Once the NGINX Ingress Controller is set up, you can create Ingress resources to define how HTTP/S traffic should be routed to your services.

### Example 1: Basic Ingress Resource

This is a simple example where an Ingress resource routes traffic from the URL path `/` to a service called `my-service` running on port 80.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
  namespace: default
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
```

### Explanation:
- `host`: Specifies the domain for the incoming traffic (`my-app.example.com`).
- `http.paths`: Defines the routing rules. In this case, it directs all traffic with the `/` path to the `my-service` service.

### Use Case for Basic Ingress:
- This is useful for simple applications where all traffic should be directed to a single service, such as a single web application or API.

---

### Example 2: Ingress with Path-based Routing

In a production-grade environment, you may want to route traffic to different services based on the URL path.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-path-ingress
  namespace: default
spec:
  rules:
  - host: my-app.example.com
    http:
      paths:
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80
      - path: /frontend
        pathType: Prefix
        backend:
          service:
            name: frontend-service
            port:
              number: 80
```

### Explanation:
- Traffic to `my-app.example.com/api` is routed to `api-service`, while traffic to `my-app.example.com/frontend` is routed to `frontend-service`.

### Use Case for Path-based Routing:
- This is useful in microservice architectures where different services need to handle different URL paths. For example, `/api` could route to a backend service, while `/frontend` could route to a web application.

---

### Example 3: Ingress with TLS Termination

In production environments, you often need to secure traffic with TLS. Here is an example of an Ingress resource that uses TLS termination.

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
- `tls`: Specifies that the `my-app.example.com` host should use TLS termination, with the TLS certificate stored in a secret called `my-tls-secret`.
- `nginx.ingress.kubernetes.io/ssl-redirect`: This annotation forces HTTP traffic to be redirected to HTTPS.

### Use Case for TLS Termination:
- This is a critical use case for production-grade environments where all external traffic should be encrypted using HTTPS to ensure data security and integrity.

---

### Example 4: Ingress with Authentication

For sensitive applications, you might want to add basic authentication to your Ingress resource. Here’s how you can secure an Ingress resource with basic authentication.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-auth-ingress
  namespace: default
  annotations:
    nginx.ingress.kubernetes.io/auth-type: basic
    nginx.ingress.kubernetes.io/auth-secret: my-auth-secret
    nginx.ingress.kubernetes.io/auth-realm: "Protected Area"
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
```

### Explanation:
- This Ingress requires basic authentication using the credentials stored in the `my-auth-secret` secret.
- `nginx.ingress.kubernetes.io/auth-type: basic`: Specifies the use of basic authentication.

### Use Case for Authentication:
- Useful for securing applications or services that need to be accessed by a limited set of users, such as internal tools or administrative dashboards.

---

## 3. Production-Grade Use Cases for NGINX Ingress

### Use Case 1: Microservices Architecture

In a production environment with multiple microservices, NGINX Ingress Controller helps route external traffic to various microservices based on the URL path or subdomain. For example, the `frontend-service` handles web traffic, while the `api-service` handles API requests.

### Use Case 2: Centralized Traffic Management

Using NGINX Ingress Controller allows you to centralize traffic management, including SSL/TLS termination, authentication, and rate limiting, in one place. This simplifies application deployment and security management in Kubernetes.

---

### Conclusion

The NGINX Ingress Controller is a powerful tool for managing external traffic to Kubernetes applications. By using Helm, you can easily install it, and with Ingress resources, you can define how traffic should be routed to your services. In production environments, using advanced features like TLS termination, path-based routing, and authentication will help you manage and secure your applications efficiently.
