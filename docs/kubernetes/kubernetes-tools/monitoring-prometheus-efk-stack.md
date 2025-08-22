---
description: Kubernetes Prometheus Stack & ElasticSearch Fluentd Kibana Stack
---

# Kubernetes Monitoring - Prometheus, EFK Stack

![](<../images/kubernetes_prometheus.png>)

## Prometheus Stack

There are 4 different places we need to monitor in Kubernetes:

1. How is the K8s Cluster working? What objects are there?
2. What is the current status of objects? EX: Are the replicas we wrote in Deployment really created?
3. We need to monitor Nodes. We run containers on worker nodes. How are the CPU and memory usage, traffic of these
   nodes?
4. Logs are generated inside containers. How will we read these logs?

### Without using any monitoring tool;

–> I can learn the status of existing Pods with the `kubectl get pods` command.

–> I can learn the status of all objects in the system with **`kubectl get all -A`**.

–> For a specific object (EX: a pod), I can learn with `kubectl describe podName`.

–> I can learn what events have occurred in a cluster from the beginning with **`kubectl get events -A`**. (`-A` brings
us all namespaces.)

–> I can see CPU and Memory usage with `kubectl top node`. (If I say Pod, we can see the pod's.)

–> I can access logs with `kubectl logs <podName>`.

Although we can get what we want manually with such commands, it's easier and more logical to manage this from a central
place. When an error occurs, the Alert mechanism should come into play and send me an email. We can manage all of these
with Prometheus. (The first 3)

### What is Prometheus?

–> It's a metrics server. It's used almost all over the world. CNCF project (like k8s).

–> Pull based operation: You do the installation, Prometheus collects the necessary metrics itself.

–> It doesn't only work with k8s, for example you can also use it in Frontend.

* **Kubernetes Metrics** pulls the current status of objects by talking to the k8s API and sends them to Prometheus.
* **Node Exporter** pulls the status of Nodes and sends them to Prometheus.
* Prometheus can talk directly with the **Kubernetes API**. It can learn the status of the cluster.
* We can run Query in Prometheus. But I need to visualize this information, I need to create dashboards. We can provide
  this with **Grafana**.
* Installation and integration of Prometheus and other tools is normally very tedious and complex. For this reason,
  prometheus-community created a stack under the name **helm-charts** that will facilitate this situation. Let's move to
  stack installation..

### Installation

* Let's create a namespace named **monitoring**:

```shell
kubectl create namespace monitoring
```

* Let's install **kubectl-prometheus-stack**:

```shell
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install kubeprostack --namespace monitoring prometheus-community/kube-prometheus-stack
```

* Let's make sure it's installed:

```shell
kubectl get pods -n monitoring

# Output:
NAME                                                     READY   STATUS    RESTARTS   AGE
alertmanager-kubeprostack-kube-promethe-alertmanager-0   2/2     Running   0          15m
kubeprostack-grafana-5c5d98864b-dn2jz                    3/3     Running   0          15m
kubeprostack-kube-promethe-operator-5fcb5784fc-57pvs     1/1     Running   0          15m
kubeprostack-kube-state-metrics-5765b49669-6qqgl         1/1     Running   0          15m
kubeprostack-prometheus-node-exporter-82mcn              1/1     Running   0          15m
prometheus-kubeprostack-kube-promethe-prometheus-0       2/2     Running   0          15m
```

* Many pods like grafana are exposed in the installation. (Opened to the outside world) However, prometheus is not. For
  this, we need to do **port-forwarding**. Thus, we will be able to connect to prometheus from the web interface.

```shell
kubectl --namespace monitoring port-forward svc/kubeprostack-kube-promethe-prometheus 9090

# then we can go to http://localhost:9090.
```

* Let's go to the `http://localhost:9090` Prometheus UI screen and run a few queries:

```shell
kube_pod_created # Shows all pods created so far.

count by (namespace) (kube_pod_created) # Distribution by namespace 

sum by (namespace) (kube_pod_info) # Currently running pods

sum by (namespace) (kube_pod_status_ready{condition="false"}) # Pods not in Ready state "distribution by namespace"
```

* Let's check **Grafana**:

```shell
kubectl --namespace monitoring port-forward svc/kubeprostack-grafana 8080:80

# user: admin
# pw: prom-operator

# To get Grafana password:
kubectl get secret kubeprostack-grafana -n monitoring -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

* Let's check **Alert Manager**:

```shell
kubectl --namespace monitoring port-forward svc/kubeprostack-kube-promethe-alertmanager 9093
```

## EFK Stack (ElasticSearch Fluentd Kibana)

It's the stack used for **Logging**.

* ElasticSearch is where logs are collected and stored.
* **Kibana** is used to visualize data taken from ElasticSearch. (_Like Grafana in a way._)
* **LogStash** (_ElasticSearch product_) and **Fluentd** (_CNCF project_) are tools responsible for collecting logs.
* _FluentD is more performant than LogStash._

### Installation (on minikube)

The installation here is based on **minikube**, and yaml files have been prepared to change some settings. Although
installation on Cloud is easily done with **helm**, when we use helm in minikube installation, we encounter some errors.

* Let's start minikube and activate the relevant storage addons:

```shell
minikube start --cpus 4 --memory 6144

minikube addons enable default-storageclass
minikube addons enable storage-provisioner
```

* Let's create a pod that will continuously **generate logs**: (`testpod.yaml`)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: loggenerator
spec:
  containers:
    - name: loggenerator
      image: busybox
      args: [ /bin/sh, -c,'i=0; while true; do echo "Test Log $i"; i=$((i+1)); sleep 1; done' ]
```

* Let's create a new namespace named **efk**:

```shell
$ kubectl create namespace efk
```

* Let's create the ElasticSearch cluster:

<details>
<summary>elastic.yaml</summary>

```yaml
kind: Service
apiVersion: v1
metadata:
  name: elasticsearch
  namespace: efk
  labels:
    app: elasticsearch
spec:
  selector:
    app: elasticsearch
  clusterIP: None
  ports:
    - port: 9200
      name: rest
    - port: 9300
      name: inter-node
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: es-cluster
  namespace: efk
spec:
  serviceName: elasticsearch
  replicas: 3
  selector:
    matchLabels:
      app: elasticsearch
  template:
    metadata:
      labels:
        app: elasticsearch
    spec:
      containers:
        - name: elasticsearch
          image: docker.elastic.co/elasticsearch/elasticsearch:7.16.0
          resources:
            limits:
              cpu: 1000m
            requests:
              cpu: 100m
          ports:
            - containerPort: 9200
              name: rest
              protocol: TCP
            - containerPort: 9300
              name: inter-node
              protocol: TCP
          volumeMounts:
            - name: data
              mountPath: /usr/share/elasticsearch/data
          env:
            - name: cluster.name
              value: k8s-logs
            - name: node.name
              valueFrom:
                fieldRef:
                  fieldPath: metadata.name
            - name: discovery.seed_hosts
              value: "es-cluster-0.elasticsearch,es-cluster-1.elasticsearch,es-cluster-2.elasticsearch"
            - name: cluster.initial_master_nodes
              value: "es-cluster-0,es-cluster-1,es-cluster-2"
            - name: ES_JAVA_OPTS
              value: "-Xms512m -Xmx512m"
      initContainers:
        - name: fix-permissions
          image: busybox
          command: [ "sh", "-c", "chown -R 1000:1000 /usr/share/elasticsearch/data" ]
          securityContext:
            privileged: true
          volumeMounts:
            - name: data
              mountPath: /usr/share/elasticsearch/data
        - name: increase-vm-max-map
          image: busybox
          command: [ "sysctl", "-w", "vm.max_map_count=262144" ]
          securityContext:
            privileged: true
        - name: increase-fd-ulimit
          image: busybox
          command: [ "sh", "-c", "ulimit -n 65536" ]
          securityContext:
            privileged: true
  volumeClaimTemplates:
    - metadata:
        name: data
        labels:
          app: elasticsearch
      spec:
        accessModes: [ "ReadWriteOnce" ]
        storageClassName: standard
        resources:
          requests:
            storage: 1Gi
```

</details>

```shell
kubectl apply -f elastic.yaml
```

## References

- [GitHub - Prometheus Community Helm Charts](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack)
- [GitHub - Aytitech K8sFundamentals](https://github.com/aytitech/k8sfundamentals/tree/main/monitoring)