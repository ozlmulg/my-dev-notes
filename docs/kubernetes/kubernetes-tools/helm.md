# Helm

## Overview

Helm is the package manager for Kubernetes. It simplifies the deployment and management of Kubernetes applications by
packaging them into charts - a collection of files that describe a related set of Kubernetes resources.

## Installation

### macOS (Homebrew)

```bash
brew install helm
```

### Linux

```bash
curl https://get.helm.sh/helm-v3.12.0-linux-amd64.tar.gz | tar xz
sudo mv linux-amd64/helm /usr/local/bin/helm
```

### Windows

```bash
# Using Chocolatey
choco install kubernetes-helm

# Using Scoop
scoop install helm
```

### Verify Installation

```bash
helm version
```

## Basic Concepts

- **Chart**: A Helm package containing all resource definitions necessary to run an application
- **Repository**: A collection of charts that can be shared
- **Release**: An instance of a chart running in a Kubernetes cluster
- **Values**: Configuration parameters that customize the chart

## Repository Management

### Add Official Helm Repository

```bash
helm repo add stable https://charts.helm.sh/stable
helm repo add bitnami https://charts.bitnami.com/bitnami
```

### Update Repository

```bash
helm repo update
```

### List Repositories

```bash
helm repo list
```

## Chart Operations

### Search Charts

```bash
helm search repo nginx
helm search repo stable/nginx
```

### Install Chart

```bash
# Install with default values
helm install my-release stable/nginx

# Install with custom values file
helm install my-release stable/nginx -f values.yaml

# Install with specific values
helm install my-release stable/nginx --set replicaCount=3
```

### List Releases

```bash
helm list
helm list -n <namespace>
```

### Upgrade Release

```bash
helm upgrade my-release stable/nginx
helm upgrade my-release stable/nginx --set replicaCount=5
```

### Rollback Release

```bash
helm rollback my-release 1
```

### Uninstall Release

```bash
helm uninstall my-release
```

## Creating Custom Charts

### Create New Chart

```bash
helm create my-chart
```

This creates a directory structure:

```
my-chart/
├── Chart.yaml
├── values.yaml
├── charts/
├── templates/
│   ├── _helpers.tpl
│   ├── deployment.yaml
│   ├── service.yaml
│   └── NOTES.txt
└── .helmignore
```

### Chart.yaml Example

```yaml
apiVersion: v2
name: my-application
description: A Helm chart for my application
type: application
version: 0.1.0
appVersion: "1.0.0"
keywords:
  - web
  - application
home: https://github.com/my-org/my-application
sources:
  - https://github.com/my-org/my-application
maintainers:
  - name: Your Name
    email: your.email@example.com
```

### values.yaml Example

<details>
<summary>values.yaml</summary>

```yaml
# Default values for my-application
replicaCount: 1

image:
  repository: nginx
  pullPolicy: IfNotPresent
  tag: "1.19"

imagePullSecrets: [ ]
nameOverride: ""
fullnameOverride: ""

serviceAccount:
  create: true
  annotations: { }
  name: ""

podAnnotations: { }

podSecurityContext: { }

securityContext: { }

service:
  type: ClusterIP
  port: 80

ingress:
  enabled: false
  className: ""
  annotations: { }
  hosts:
    - host: chart-example.local
      paths:
        - path: /
          pathType: ImplementationSpecific
  tls: [ ]

resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi

autoscaling:
  enabled: false
  minReplicas: 1
  maxReplicas: 100
  targetCPUUtilizationPercentage: 80
```

</details>

### Template Example (templates/deployment.yaml)

<details>
<summary>deployment.yaml</summary>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: { { include "my-application.fullname" . } }
  labels:
    { { - include "my-application.labels" . | nindent 4 } }
spec:
  { { - if not .Values.autoscaling.enabled } }
  replicas: { { .Values.replicaCount } }
  { { - end } }
  selector:
    matchLabels:
      { { - include "my-application.selectorLabels" . | nindent 6 } }
  template:
    metadata:
      { { - with .Values.podAnnotations } }
      annotations:
        { { - toYaml . | nindent 8 } }
      { { - end } }
      labels:
        { { - include "my-application.selectorLabels" . | nindent 8 } }
    spec:
      { { - with .Values.imagePullSecrets } }
      imagePullSecrets:
        { { - toYaml . | nindent 8 } }
      { { - end } }
      serviceAccountName: { { include "my-application.serviceAccountName" . } }
      securityContext:
        { { - toYaml .Values.podSecurityContext | nindent 8 } }
      containers:
        - name: { { .Chart.Name } }
          securityContext:
            { { - toYaml .Values.securityContext | nindent 12 } }
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag | default .Chart.AppVersion }}"
          imagePullPolicy: { { .Values.image.pullPolicy } }
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
            { { - toYaml .Values.resources | nindent 12 } }
      { { - with .Values.nodeSelector } }
      nodeSelector:
        { { - toYaml . | nindent 8 } }
      { { - end } }
      { { - with .Values.affinity } }
      affinity:
        { { - toYaml . | nindent 8 } }
      { { - end } }
      { { - with .Values.tolerations } }
      tolerations:
        { { - toYaml . | nindent 8 } }
      { { - end } }
```

</details>

## Advanced Usage

### Using Values Files

Create a custom values file (`custom-values.yaml`):

<details>
<summary>custom-values.yaml</summary>

```yaml
replicaCount: 3
image:
  repository: my-registry/my-app
  tag: "2.0.0"
service:
  type: LoadBalancer
  port: 8080
resources:
  limits:
    cpu: 500m
    memory: 512Mi
  requests:
    cpu: 250m
    memory: 256Mi
```

</details>

Install with custom values:

```bash
helm install my-release ./my-chart -f custom-values.yaml
```

### Override Values

```bash
helm install my-release ./my-chart \
  --set replicaCount=5 \
  --set image.tag=latest \
  --set service.type=NodePort
```

### Template Rendering

Preview the rendered templates without installing:

```bash
helm template my-release ./my-chart
helm template my-release ./my-chart -f values.yaml
```

### Package Chart

```bash
helm package ./my-chart
```

### Install from Package

```bash
helm install my-release my-application-0.1.0.tgz
```

## Best Practices

### Chart Structure

- Use meaningful names for charts and releases
- Include comprehensive documentation in README.md
- Use semantic versioning for chart versions
- Provide sensible defaults in values.yaml
- Use templates for reusable components

### Security

- Avoid hardcoding secrets in templates
- Use Kubernetes secrets for sensitive data
- Implement proper RBAC configurations
- Use security contexts in pod specifications

### Testing

```bash
# Lint chart
helm lint ./my-chart

# Test chart installation
helm test my-release

# Validate chart dependencies
helm dependency build ./my-chart
```

### Dependencies

Add dependencies to `Chart.yaml`:

```yaml
dependencies:
  - name: postgresql
    version: 12.1.2
    repository: https://charts.bitnami.com/bitnami
    condition: postgresql.enabled
```

Install dependencies:

```bash
helm dependency update ./my-chart
helm dependency build ./my-chart
```

## Common Commands Reference

```bash
# Chart management
helm create <chart-name>
helm package <chart-directory>
helm lint <chart-directory>

# Repository management
helm repo add <name> <url>
helm repo update
helm repo list
helm repo remove <name>

# Release management
helm install <release-name> <chart>
helm upgrade <release-name> <chart>
helm rollback <release-name> <revision>
helm uninstall <release-name>
helm list
helm status <release-name>

# Values and configuration
helm get values <release-name>
helm get manifest <release-name>
helm get hooks <release-name>

# Template operations
helm template <release-name> <chart>
helm show values <chart>
helm show chart <chart>
helm show readme <chart>
```

## References

- [Helm Official Documentation](https://helm.sh/docs/)
- [Helm Charts Repository](https://github.com/helm/charts)
- [Helm Hub](https://hub.helm.sh/)
- [Best Practices Guide](https://helm.sh/docs/chart_best_practices/)
