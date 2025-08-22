# Kubernetes Service Mesh

## What Is a Service Mesh?

A Service Mesh is an infrastructure layer that handles service-to-service communication for cloud‑native applications.
It moves cross‑cutting networking concerns (discovery, traffic shaping, retries, timeouts, mutual TLS, telemetry) out of
application code into the platform, giving you consistent, policy‑driven behavior without changing your services.

Key ideas:

- Data plane: lightweight proxies that sit next to your services (sidecars) and handle traffic.
- Control plane: central components that program the proxies with policies, routes, and certificates.

Benefits:

- Traffic management (routing, canary, blue/green, A/B, retries, timeouts, circuit breaking)
- Observability (golden signals, distributed tracing, topology graphs)
- Security (mTLS, fine‑grained authorization, policy)

## How It Works in Kubernetes

In Kubernetes, a Service Mesh typically injects a sidecar proxy into each Pod. The application container talks to the
local proxy; the proxy talks to other proxies. The control plane configures these proxies using Kubernetes resources (
CRDs) and watches for changes.

Flow:

1. Sidecar injection (automatic via namespace label or manual via CLI) adds a proxy container to each Pod.
2. Control plane watches the cluster, computes routes/policies, and pushes them to sidecars.
3. All inter‑service traffic traverses the sidecars, enabling uniform features without code changes.

## Popular Implementations

- Istio (Envoy‑based, rich features, broad ecosystem)
- Linkerd (ultra‑light Rust/Go proxies, opinionated, great UX)
- Consul Service Mesh (HashiCorp ecosystem)
- Kuma/Envoy (by Kong; CNCF project)

This guide will show minimal quickstarts for Istio and Linkerd so you can choose the right fit.

## The Sidecar Proxy Pattern

Each Pod gets an extra container (the sidecar) that intercepts outbound and inbound traffic:

- Outbound: app → sidecar → remote sidecar → remote app
- Inbound: remote app → remote sidecar → local sidecar → app

Because all traffic flows through proxies, the mesh can enforce mTLS, apply rate limits, inject timeouts, and collect
telemetry—without modifying your app.

---

## Quickstart: Istio

Prerequisites:

- A Kubernetes cluster
- kubectl installed and configured
- istioctl or Helm (we use istioctl for simplicity)

### 1) Install Istio (demo profile)

```shell
istioctl install --set profile=demo -y

# Verify control plane
kubectl -n istio-system get pods
```

### 2) Enable Automatic Sidecar Injection

Label a namespace to auto‑inject the sidecar.

```shell
kubectl create namespace mesh-demo
kubectl label namespace mesh-demo istio-injection=enabled
```

### 3) Deploy a Sample App (two versions)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-v1
  namespace: mesh-demo
  labels:
    app: web            # app identity used by Service selector
    version: v1         # version label for routing
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
      version: v1
  template:
    metadata:
      labels:
        app: web
        version: v1
    spec:
      containers:
        - name: web
          image: hashicorp/http-echo:1.0
          args: [ "-text=Hello from v1" ]
          ports:
            - containerPort: 5678       # app port
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-v2
  namespace: mesh-demo
  labels:
    app: web
    version: v2
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
      version: v2
  template:
    metadata:
      labels:
        app: web
        version: v2
    spec:
      containers:
        - name: web
          image: hashicorp/http-echo:1.0
          args: [ "-text=Hello from v2" ]
          ports:
            - containerPort: 5678
---
apiVersion: v1
kind: Service
metadata:
  name: web
  namespace: mesh-demo
spec:
  selector:
    app: web
  ports:
    - name: http
      port: 80              # cluster-facing port
      targetPort: 5678      # forwards to containerPort
```

Explanation highlights:

- labels.app/labels.version: used by Istio routing to target subsets
- Service targets all versions; routing decides traffic split

Apply:

```shell
kubectl apply -f web.yaml
```

### 4) Traffic Management (Istio)

Define subsets and a canary split using DestinationRule + VirtualService.

```yaml
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: web
  namespace: mesh-demo
spec:
  host: web.mesh-demo.svc.cluster.local  # FQDN of the Service
  trafficPolicy:
    connectionPool:
      http:
        http1MaxPendingRequests: 100     # queueing before 503s
        maxRequestsPerConnection: 100
    outlierDetection:
      consecutive5xxErrors: 5            # circuit breaking trigger
      interval: 5s
      baseEjectionTime: 30s
  subsets:
    - name: v1
      labels:
        version: v1                        # matches Deployment label
    - name: v2
      labels:
        version: v2
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: web
  namespace: mesh-demo
spec:
  hosts:
    - web
  http:
    - retries:
        attempts: 2                         # retry failed requests
        perTryTimeout: 1s
      timeout: 3s                            # overall request timeout
      route:
        - destination:
            host: web
            subset: v1
          weight: 90
        - destination:
            host: web
            subset: v2
          weight: 10
```

Key fields:

- DestinationRule.subsets: group Pods by version for routing
- trafficPolicy: connection pools, circuit breaking, TLS, etc.
- VirtualService.http.route: percentage split for canary
- retries/timeout: app‑agnostic resiliency

### 5) mTLS and Authorization (Istio)

Enable mesh‑wide mTLS in STRICT mode and allow only in‑namespace traffic to the `web` Service.

```yaml
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
  name: default
  namespace: mesh-demo
spec:
  mtls:
    mode: STRICT        # require mTLS for inbound traffic
---
apiVersion: security.istio.io/v1beta1
kind: AuthorizationPolicy
metadata:
  name: web-allow-same-namespace
  namespace: mesh-demo
spec:
  selector:
    matchLabels:
      app: web
  rules:
    - from:
        - source:
            namespaces: [ "mesh-demo" ]   # only callers in this namespace
      to:
        - operation:
            ports: [ "80" ]               # limit allowed ports
```

Test calls (from a test Pod):

```shell
kubectl -n mesh-demo run curl --image=curlimages/curl --restart=Never -it -- /bin/sh
curl -s web
```

Observability tips:

- Istio installs Prometheus/Grafana/Jaeger in some profiles or exposes telemetry via CRDs—check `istio-system` for
  addons.

Uninstall (optional):

```shell
istioctl uninstall -y
kubectl delete namespace istio-system
```

---

## Quickstart: Linkerd

Prerequisites:

- A Kubernetes cluster + kubectl
- Linkerd CLI: `curl -sL https://run.linkerd.io/install | sh`

### 1) Install and Validate

```shell
linkerd check --pre
linkerd install | kubectl apply -f -
linkerd check
```

### 2) Meshed Namespace and App

```shell
kubectl create namespace l5d-demo
kubectl annotate namespace l5d-demo linkerd.io/inject=enabled

kubectl -n l5d-demo apply -f - <<'YAML'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
  labels:
    app: web
spec:
  replicas: 2
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: web
        image: hashicorp/http-echo:1.0
        args: ["-text=Hello from Linkerd"]
        ports:
        - containerPort: 5678
---
apiVersion: v1
kind: Service
metadata:
  name: web
spec:
  selector:
    app: web
  ports:
  - name: http
    port: 80
    targetPort: 5678
YAML
```

### 3) Traffic Split (SMI API)

Linkerd supports SMI for progressive delivery. Here’s an example splitting traffic between two backends.

```yaml
apiVersion: split.smi-spec.io/v1alpha2
kind: TrafficSplit
metadata:
  name: web-split
  namespace: l5d-demo
spec:
  service: web                      # apex Service that clients call
  backends:
    - service: web-v1                 # Service pointing to v1 Deployment
      weight: 90
    - service: web-v2                 # Service pointing to v2 Deployment
      weight: 10
```

Notes:

- You create `web-v1` and `web-v2` Services each selecting a distinct version label.
- Adjust weights to roll traffic gradually.

### 4) Observability

```shell
linkerd viz install | kubectl apply -f -
linkerd viz dashboard
```

Linkerd surfaces per‑route golden signals (latency, success rate, RPS) with negligible overhead.

### 5) mTLS and Policy

Linkerd enables mTLS by default. To restrict traffic, use Linkerd Policy resources (Server, ServerAuthorization) or
Kubernetes NetworkPolicy for additional control.

Uninstall (optional):

```shell
linkerd viz uninstall | kubectl delete -f -
linkerd uninstall | kubectl delete -f -
```

---

## Clean Up

```shell
kubectl delete namespace mesh-demo || true
kubectl delete namespace l5d-demo || true
```

---

## References

- [Istio Official Website](https://istio.io/latest/docs/setup/getting-started/)
- [GitHub - Aytitech K8sFundamentals](https://github.com/aytitech/k8sfundamentals/tree/main/servicemesh)