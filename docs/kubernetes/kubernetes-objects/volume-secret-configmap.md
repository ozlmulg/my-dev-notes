# Kubernetes Objects - Volume, Secret, ConfigMap

## Volume

* Container files are ephemeral and exist only while the container is running. When containers are deleted, these files are also removed. Each time a new container is created, container-specific files are recreated from scratch. (This represents the **stateless concept**)
* In certain scenarios, these files need to persist beyond container lifecycle. This is where **Ephemeral (Temporary) Volume concepts** become relevant. (This represents the **stateful concept**) For example, to prevent data loss in database containers, **volume structures** should be implemented.
* Ephemeral Volumes can be mounted to all containers within the same Pod simultaneously.
* Another purpose is to create shared storage areas that multiple containers in the same Pod can utilize collaboratively.

> **Important Note:** In Ephemeral Volumes, if the Pod is deleted, all data is lost. However, if only the container is deleted and recreated, data persists as long as the Pod remains intact.

There are two types of Ephemeral Volumes:

### emptyDir Volume

![](../images/kubernetes_emptydir.png)

Sample YAML file to create an emptyDir volume:

<details>
<summary>emptydir.yaml</summary>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: emptydir
spec:
  containers:
    - name: frontend
      image: ozlmulg/k8s:blue
      ports:
        - containerPort: 80
      livenessProbe:
        httpGet:
          path: /healthcheck
          port: 80
        initialDelaySeconds: 5
        periodSeconds: 5
      volumeMounts:
        - name: cache-vol # Provides connection with volume
          mountPath: /cache # Mount point within the container
    - name: sidecar
      image: busybox
      command: [ "/bin/sh" ]
      args: [ "-c", "sleep 3600" ]
      volumeMounts:
        - name: cache-vol
          mountPath: /tmp/log # Mount point within the container
  volumes:
    - name: cache-vol # First we create the volume, then we mount it to containers
      emptyDir: { }
```

</details>

### hostPath Volume

* **Usage Warning:** This volume type is rarely used and requires careful consideration when implemented.
* While emptyDir creates volume folders within the Pod, hostPath creates volume folders on the worker node itself.
* Three different types are supported:
    * **Directory** → Used for folders that already exist on the worker node.
    * **DirectoryOrCreate** → Used for existing folders or to create the folder if it doesn't exist.
    * **FileOrCreate** → Not a folder! Used for single files. If the file doesn't exist, it's created.

![](../images/kubernetes_hostpath.png)

Sample YAML file to create a hostPath volume:

<details>
<summary>hostpath.yaml</summary>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hostpath
spec:
  containers:
    - name: hostpathcontainer
      image: ozlmulg/k8s:blue
      ports:
        - containerPort: 80
      livenessProbe:
        httpGet:
          path: /healthcheck
          port: 80
        initialDelaySeconds: 5
        periodSeconds: 5
      volumeMounts:
        - name: directory-vol
          mountPath: /dir1
        - name: dircreate-vol
          mountPath: /cache
        - name: file-vol
          mountPath: /cache/config.json
  volumes:
    - name: directory-vol
      hostPath:
        path: /tmp
        type: Directory
    - name: dircreate-vol
      hostPath:
        path: /cache
        type: DirectoryOrCreate
    - name: file-vol
      hostPath:
        path: /cache/config.json
        type: FileOrCreate
```

</details>

## Secret

* Although sensitive information (database credentials, etc.) can be stored in Environment Variables, this approach may not be ideal for security and management purposes.
* The Secret object allows us to separate sensitive information from application object definitions in YAML files and manage them independently.
* It's always safer and more flexible to store sensitive data such as tokens, usernames, and passwords in Secret objects.

### Creating Declarative Secrets

* **Important:** The Secret and the Pods it will be assigned to **must be in the same namespace.**
* Eight different types of secrets can be created. **`Opaque`** is a generic type that can store almost all sensitive data.

Example secret.yaml file:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
stringData: # Sensitive data is written under stringData
  db_server: db.example.com
  db_username: admin
  db_password: P@ssw0rd!
```

To view the data in the secret:

```shell
kubectl describe secrets <secretName>
```

### Creating Imperative Secrets

```shell
kubectl create secret generic <secretName> --from-literal=db_server=db.example.com --from-literal=db_username=admin
```

⚠️ **Note:** `generic` here corresponds to `Opaque` type specified in YAML.

**Alternative Approach:** If you prefer not to enter sensitive data via CLI, you can store each piece of data in separate `.txt` files and use the `--from-file=db_server=server.txt` command. You can also use `.json` files instead of `.txt` files by specifying `--from-file=config.json`.

JSON example:

```json
{
  "apiKey": "9bxa108d4b2212f2c30c71dfa279e1f77cc5c3b1"
}
```

### Reading Secrets from Pods

There are **two methods** to transfer created Secrets to Pods:

**Method 1: Volume Mount** and **Method 2: Environment Variables**

Both methods are demonstrated in the YAML file below:

<details>
<summary>secretpodvolume.yaml</summary>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secretpodvolume
spec:
  containers:
    - name: secretcontainer
      image: ozlmulg/k8s:blue
      volumeMounts: # 2) We include the created secret volume in the pod
        - name: secret-vol
          mountPath: /secret # 3) The /secret folder in the application is mounted to the volume
          # Now we can access this file from within the application and read the values
  volumes: # 1) First we create the volume and include the secret in the volume
    - name: secret-vol
      secret:
        secretName: mysecret3
        # When we enter this pod with exec, we will see a secret folder under root. The file names here are "KEYs", the values inside are "VALUEs"
---
apiVersion: v1
kind: Pod
metadata:
  name: secretpodenv
spec:
  containers:
    - name: secretcontainer
      image: ozlmulg/k8s:blue
      env: # We can define all secrets as environment variables in the pod
        # In this method, we defined all secrets and their values individually
        - name: username
          valueFrom:
            secretKeyRef:
              name: mysecret3 # Take the value with "db_username" key from the secret named mysecret3
              key: db_username
        - name: password
          valueFrom:
            secretKeyRef:
              name: mysecret3
              key: db_password
        - name: server
          valueFrom:
            secretKeyRef:
              name: mysecret3
              key: db_server
---
apiVersion: v1
kind: Pod
metadata:
  name: secretpodenvall
spec:
  containers:
    - name: secretcontainer
      image: ozlmulg/k8s:blue
      envFrom: # Same as method 2, only difference is we define all secrets at once
        - secretRef:
            name: mysecret3
```

</details>

**Viewing All Environment Variables in Pods**

```shell
kubectl exec <podName> -- printenv
```

## ConfigMap

* ConfigMaps operate using exactly the same logic as Secret objects. The key difference is that Secrets are stored encrypted in etcd using base64 encoding, while ConfigMaps are not encrypted and therefore should not contain sensitive data.
* ConfigMaps can be defined as **Volumes** or **Environment Variables** in Pods.
* Since creation methods are identical to Secrets, the commands above remain valid.

<details>
<summary>configmap.yaml</summary>

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: myconfigmap
data: # Should be entered in Key-Value format
  db_server: "db.example.com"
  database: "mydatabase"
  site.settings: | # "|" is used for multi-line writing
    color=blue
    padding:25px
---
apiVersion: v1
kind: Pod
metadata:
  name: configmappod
spec:
  containers:
    - name: configmapcontainer
      image: ozlmulg/k8s:blue
      env:
        - name: DB_SERVER
          valueFrom:
            configMapKeyRef:
              name: myconfigmap
              key: db_server
        - name: DATABASE
          valueFrom:
            configMapKeyRef:
              name: myconfigmap
              key: database
      volumeMounts:
        - name: config-vol
          mountPath: "/config"
          readOnly: true
  volumes:
    - name: config-vol
      configMap:
        name: myconfigmap
```

</details>

### Creating ConfigMaps from Configuration Files

Consider scenarios where you have `config.qa.yaml` or `config.prod.json` files for different environments (QA, SIT, and PROD) to be used in your application. How can you create ConfigMaps from the appropriate configuration file based on these environments in CI/CD?

<details>
<summary>config.json</summary>

```json
{
  "name": "TestName",
  "surName": "TestSurname",
  "email": "test@testmail.com",
  "apiKey": "9bxa108d4b2212f2c30c71dfa279e1f77cc5c3b1",
  "text": [
    "test",
    "example",
    "one",
    "two",
    3,
    true
  ]
}
```

</details>

1. **Creating a ConfigMap from our config.json file via Kubectl:**

   **If you're running in CI/CD and want to track logs:**

```shell
# --dry-run is normally deprecated, but still used in older versions
kubectl create configmap xyzconfig --from-file ${configFile} -o yaml --dry-run | kubectl apply -f -

# New version --dry-run="client"
kubectl create configmap testconfig --from-file config.json -o yaml --dry-run="client" | kubectl apply -f -

# The "-" (dash) at the end takes the output from the first part of the pipe
```

* When you run the command above, kubectl takes the config.json file, creates ConfigMap YAML content that can be used with the `kubectl apply` command, and **prints this content to the screen as output** due to the **`--dry-run`** option.
* We capture the incoming output (using bash "pipe | ") and send it to our cluster with the `kubectl apply` command to create the ConfigMap.

**If you want to create a ConfigMap directly from the config.json file without reading logs:**

```shell
# You can use many format files instead of config.json: e.g., yaml
kubectl create configmap <configName> --from-file config.json
```

2. **Now when creating the Pod, we need to transfer the values in our ConfigMap to a file in a folder inside the Pod and store it as a file using "volume" logic.**

* Let's define our volume and configMap in the "volumes" section.
* In the "volumeMounts" section inside the Pod, let's introduce this volume to our pod.
    * In the **`mountPath`** section, we can specify under which folder the file in the configMap will be copied inside the Pod and what its new name will be.
    * With **`subPath`**, we need to provide the name of the file in the configMap (e.g., `config.json`). Thus, when the Pod is created, we specify "This file's name will change."

<details>
<summary>configmappod4.yaml</summary>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: configmappod4
spec:
  containers:
    - name: configmapcontainer
      image: ozlmulg/k8s:blue
      volumeMounts:
        - name: config-vol
          mountPath: "/config/newconfig.json"
          subPath: "config.json"
          readOnly: true
  volumes:
    - name: config-vol
      configMap:
        name: test-config # This ConfigMap contains the config.json file
```

</details>

#### **Alternative Volume Definition Syntax**

We can also write the volumes section in a different way:

<details>
<summary>configmappod.yaml</summary>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: configmappod
spec:
  containers:
    - name: configmapcontainer
      image: ozlmulg/k8s:blue
      volumeMounts:
        - name: config-vol
          mountPath: "/config/newconfig.json"
          subPath: "config.json"
          readOnly: true
  volumes:
    - name: config-vol
      projected:
        sources:
          - configMap:
              name: test-config
              items:
                - key: config.json
                  path: config.json
```

</details>

## References

- [Kubernetes Official Volume Overview](https://kubernetes.io/docs/concepts/storage/volumes/)
- [Kubernetes Official Secrets Overview](https://kubernetes.io/docs/concepts/configuration/secret/)
- [Kubernetes Official Configmaps Overview](https://kubernetes.io/docs/concepts/configuration/configmap/)
- [GitHub - Aytitech K8sFundamentals - Volume](https://github.com/aytitech/k8sfundamentals/tree/main/volume)
- [GitHub - Aytitech K8sFundamentals - Secret/Configmap](https://github.com/aytitech/k8sfundamentals/tree/main/secretconfigmap)