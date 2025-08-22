---
description: Kubernetes NetworkPolicy - Core Concepts, Commands, YAML Configuration, and Practical Examples
---

# Kubernetes Objects - Network Policy

## Overview

Network Policies in Kubernetes control how Pods communicate with each other and other network endpoints. They provide
fine-grained control over network traffic within the cluster.

## Purpose

Network Policies are used for:

* **Traffic Control**: Allow or deny specific network traffic
* **Security**: Isolate Pods and restrict communication
* **Compliance**: Meet security requirements and regulations
* **Multi-tenancy**: Separate network traffic between different teams

## Basic Network Policy

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny
  namespace: default
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
```

## Allow Specific Traffic

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-web
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: web
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 80
```

## Deny All Traffic

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
  namespace: default
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
  ingress: []
  egress: []
```

## Allow from Namespace

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-from-namespace
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: database
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: frontend
    ports:
    - protocol: TCP
      port: 3306
```

## Allow Specific IP Ranges

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-ip-range
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes:
  - Ingress
  ingress:
  - from:
    - ipBlock:
        cidr: 10.0.0.0/24
        except:
        - 10.0.0.1
    ports:
    - protocol: TCP
      port: 8080
```

## Egress Policy

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-egress
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: restricted
  policyTypes:
  - Egress
  egress:
  - to:
    - namespaceSelector: {}
    ports:
    - protocol: TCP
      port: 53
    - protocol: UDP
      port: 53
  - to:
    - ipBlock:
        cidr: 0.0.0.0/0
    ports:
    - protocol: TCP
      port: 443
```

## Basic Commands

```shell
# Create Network Policy
kubectl apply -f network-policy.yaml

# List Network Policies
kubectl get networkpolicies
kubectl get netpol

# Check Network Policy status
kubectl describe networkpolicy <policy-name>

# Delete Network Policy
kubectl delete networkpolicy <policy-name>

# Check Network Policy YAML
kubectl get networkpolicy <policy-name> -o yaml
```

## Common Use Cases

### Database Access Control

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: database-access
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: database
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: web
    ports:
    - protocol: TCP
      port: 3306
```

### API Gateway Policy

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: api-gateway
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: api-gateway
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 8080
```

## References

- [Kubernetes Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
- [GitHub - Aytitech K8sFundamentals - NetworkPolicy](https://github.com/aytitech/k8sfundamentals/tree/main/networkpolicy)
