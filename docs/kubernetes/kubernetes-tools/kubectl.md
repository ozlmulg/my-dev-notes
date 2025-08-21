# Kubectl

## Overview

- **kubectl** is the command-line interface for interacting with a Kubernetes cluster.
- kubectl reads connection details from a kubeconfig file. By default, this is `~/.kube/config`. You can override the
  path with the `KUBECONFIG` environment variable.
- A kubeconfig contains definitions for clusters, users, and contexts. A **context** combines a cluster, a user, and
  optionally a namespace, and represents “connect to this cluster as this user (and namespace).”
- Tools like Minikube or managed Kubernetes providers typically generate kubeconfig entries automatically.

## Installation

- On Linux/macOS with Homebrew:

```bash
brew install kubectl
```

- Verify the client (and server, if reachable):

```bash
kubectl version
```

## Kubeconfig and Contexts

- View the current kubeconfig:

```bash
kubectl config view
```

- Show contexts defined in the current kubeconfig:

```bash
kubectl config get-contexts
```

- Show the current context:

```bash
kubectl config current-context
```

- Switch the current context:

```bash
kubectl config use-context <context-name>
```

Notes:

- In `kubectl config get-contexts`, a star (*) marks the currently active context.
- You can open and inspect your kubeconfig directly, for example with an editor: `subl ~/.kube/config`.

## Command Structure and Namespaces

kubectl commands generally follow:

```bash
kubectl <verb> <resource> [name] [flags]
# <verb> examples: get, describe, logs, exec, apply, delete, scale, rollout
# <resource> examples: pods, services, deployments, nodes, namespaces
```

- Unless overridden with `-n/--namespace`, commands operate in the namespace from the current context. If none is
  specified, the `default` namespace is used.

## Cluster and Resource Discovery

- Cluster information:

```bash
kubectl cluster-info
```

- To filter out the debug message:

```bash
 kubectl cluster-info | grep -v "To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'."
```

- List common resources:

```bash
kubectl get <object-type>

kubectl get pods
kubectl get services
kubectl get deployments
kubectl get nodes
kubectl get namespaces
```

- List a single object in the current namespace:

```bash
kubectl get <object-type> <object-name>

kubectl get pods testpod
```

- List resources in another namespace:

```bash
kubectl get <object-type> -n <namespace-name>

kubectl get pods -n testnamespace
```

- List across all namespaces:

```bash
kubectl get <object-type> --all-namespaces
kubectl get all -A               # status of many core objects cluster-wide

kubectl get pods --all-namespaces
kubectl get pods -A
```

## Output Formatting

kubectl defaults to a concise, plain-text output. Use `-o` to change the output format:

- `-o yaml` — output as YAML
- `-o json` — output as JSON
- `-o wide` — plain text with additional columns
- `-o name` — print only resource names
- `-o jsonpath=<template>` — select fields using a JSONPath template
- `-o custom-columns=<spec>` — comma-separated column definitions for tabular output

Examples:

```bash
kubectl get <object-type> -o <wide|yaml|json>

kubectl get pods -o wide
kubectl get pods -A -o json | jq -r '.items[].spec.containers[].name'
```

> Note: If using `-o json` with `jq`, you may need to install `jq` first:

```bash
# jq -> install json query plugin.
# brew install jq
```

## Explaining API Resources

Use `explain` to view schema and documentation for resources and fields:

```bash
kubectl explain <object-type>

kubectl explain pod
kubectl explain deployment.spec.template.spec.containers
```

In the output, we can understand which namespace it belongs to with **Version**.

## Help and Documentation

Use `-h/--help` to get help on any command:

```bash
kubectl <verb> --help # or -h

kubectl --help
kubectl apply --help
```

<details>
<summary>kubectl --help</summary>

```bash
kubectl controls the Kubernetes cluster manager.

 Find more information at: https://kubernetes.io/docs/reference/kubectl/

Basic Commands (Beginner):
  create          Create a resource from a file or from stdin
  expose          Take a replication controller, service, deployment or pod and
expose it as a new Kubernetes service
  run             Run a particular image on the cluster
  set             Set specific features on objects

Basic Commands (Intermediate):
  explain         Get documentation for a resource
  get             Display one or many resources
  edit            Edit a resource on the server
  delete          Delete resources by file names, stdin, resources and names, or
by resources and label selector

Deploy Commands:
  rollout         Manage the rollout of a resource
  scale           Set a new size for a deployment, replica set, or replication
controller
  autoscale       Auto-scale a deployment, replica set, stateful set, or
replication controller

Cluster Management Commands:
  certificate     Modify certificate resources
  cluster-info    Display cluster information
  top             Display resource (CPU/memory) usage
  cordon          Mark node as unschedulable
  uncordon        Mark node as schedulable
  drain           Drain node in preparation for maintenance
  taint           Update the taints on one or more nodes

Troubleshooting and Debugging Commands:
  describe        Show details of a specific resource or group of resources
  logs            Print the logs for a container in a pod
  attach          Attach to a running container
  exec            Execute a command in a container
  port-forward    Forward one or more local ports to a pod
  proxy           Run a proxy to the Kubernetes API server
  cp              Copy files and directories to and from containers
  auth            Inspect authorization
  debug           Create debugging sessions for troubleshooting workloads and
nodes
  events          List events

Advanced Commands:
  diff            Diff the live version against a would-be applied version
  apply           Apply a configuration to a resource by file name or stdin
  patch           Update fields of a resource
  replace         Replace a resource by file name or stdin
  wait            Experimental: Wait for a specific condition on one or many
resources
  kustomize       Build a kustomization target from a directory or URL

Settings Commands:
  label           Update the labels on a resource
  annotate        Update the annotations on a resource
  completion      Output shell completion code for the specified shell (bash,
zsh, fish, or powershell)

Subcommands provided by plugins:

Other Commands:
  api-resources   Print the supported API resources on the server
  api-versions    Print the supported API versions on the server, in the form of
"group/version"
  config          Modify kubeconfig files
  plugin          Provides utilities for interacting with plugins
  version         Print the client and server version information

Usage:
  kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all
commands).
```

</details>

## Watching Changes

Stream updates to resources with `-w/--watch`:

```bash
kubectl get pods -w # or --watch
watch kubectl get pods
```

## Describe, Logs, and Exec

```bash
kubectl describe pod <pod-name>
kubectl describe node <node-name>
kubectl describe deployment <deployment-name>

kubectl logs <pod-name>
kubectl exec -it <pod-name> -- /bin/bash
kubectl exec -it <pod-name> -c <container-name> -- /bin/bash
```

## Apply, Delete, Scale, and Rollouts

```bash
kubectl apply -f <file.yaml>
kubectl delete -f <file.yaml>

kubectl scale deployment <name> --replicas=<num>
kubectl rollout status deployment/<name>
kubectl rollout undo deployment/<name>
```

## Port-Forward (Local -> Pod)

–> We can open **port-forward** to directly reach objects within the k8s cluster we want from our own local servers.
This is one of the best methods to test this object.

```shell
kubectl port-forward <objectType>/<podName> <localMachinePort>:<podPort>

kubectl port-forward pod/testpod 8080:80
# Send all requests coming to port 8080 on my device to port 80 of this pod.

curl 127.0.0.1:8080
# You can write for testing.
```

Then open `localhost:8080` in the browser.
_Port-forwarding ends when CMD + C is done._

## Metrics

Requires Metrics Server installed in the cluster:

```bash
kubectl top pod
kubectl top node
```

## Quick kubeconfig Context Switch Script (optional)

If you keep multiple kubeconfig files like `~/.kube/config-minikube`, you can use a helper script to copy one into place
as the active `~/.kube/config`:

<details>
<summary>change-kube-config.sh</summary>

```bash
#! /bin/bash

CLUSTER=$1

if [ -z "$1" ]
  then
    echo -e "\n##### No argument supplied. Please select one of these configs. #####"
    ls  ~/.kube |grep config- | cut -d "-" -f 2
    echo -e "######################################################################\n"
    #array=($(ls -d * |grep config_))
    read -p 'Please set config file: ' config
    cp -r ~/.kube/config_$config ~/.kube/config
    echo -e '\n'
    kubectl cluster-info |grep -v "To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'."
    kubectl config get-contexts
    kubectl get node -o wide |head -n 4
else
  cp -r ~/.kube/config-$CLUSTER ~/.kube/config
  if [ $? -ne 0 ];
  then
  exit 1
  fi
  echo -e '\n'
#  kubectl cluster-info | grep -v "To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'."
  kubectl config get-contexts
  echo -e '\n'
  kubectl get node -o wide |head -n 4
  echo -e '\n'
fi
```

</details>

Usage:

```bash
./change-kube-config.sh <config-name>
# e.g.
./change-kube-config.sh minikube
```

## Makefile Commands for AWS EKS Setup (optional)

Make Command:

```make
aws-config-set: ## AWS configs for to up Preview env on your local machine
	@source ".make/scripts/aws-config-set.sh"
```

<details>
<summary>aws-config-set.sh</summary>

```bash
#!/bin/bash

# ======================================================================================================================
# This script performs the following tasks:
# 1. Asks to the user to select aws profile.
# 2. Removes ~/.aws/sso/cache, ~/.aws/cli/cache and ~/.kube/cache folders.
# 3. Configures aws sso start url, sso region, sso account id, sso role name, region and output.
# 4. Runs aws eks command.
# ======================================================================================================================

AWS_PROFILE_1="default"
AWS_PROFILE_2="test-dev"

echo "Which AWS profile would you like to log in to?"
echo "1) $AWS_PROFILE_1"
echo "2) $AWS_PROFILE_2"
read -p "Make your choice (1 or 2): " profile_choice

if [[ $profile_choice -eq 1 ]]; then
  SSO_ACCOUNT_ID="123456789123"
  ROLE_ARN="arn:aws:iam::123456789123:role/EKSDevV1TesterRole"
elif [[ $profile_choice -eq 2 ]]; then
  SSO_ACCOUNT_ID="987654321123"
  ROLE_ARN="arn:aws:iam::987654321123:role/EKSDevV1TesterRole"
else
  echo "Invalid choice. Exiting."
  exit 1
fi

# PARAMETERS
AWS_SSO_SESSION_NAME=""
SSO_START_URL="https://d-123407a8b9.awsapps.com/start#/"
SSO_REGION="eu-west-1"
SSO_ACCOUNT_ID="$SSO_ACCOUNT_ID"
SSO_ROLE_NAME="aws_developer"
CLI_PROFILE_NAME="default"
CLI_DEFAULT_REGION="eu-west-1"
CLI_DEFAULT_OUTPUT_FORMAT="json"
ROLE_ARN="$ROLE_ARN"

# Delete SSO cache
rm -rf ~/.aws/sso/cache

# Delete AWS CLI cache
rm -rf ~/.aws/cli/cache

# Delete kube cache
rm -rf ~/.kube/cache

# Run AWS configure sso command
{
  echo "$AWS_SSO_SESSION_NAME"
  echo "$SSO_START_URL"
  echo "$SSO_REGION"
  echo "$SSO_ACCOUNT_ID"
  echo "$SSO_ROLE_NAME"
  echo "$CLI_DEFAULT_REGION"
  echo "$CLI_DEFAULT_OUTPUT_FORMAT"
  echo "$CLI_PROFILE_NAME"
} | aws configure sso --profile "$CLI_PROFILE_NAME"

# Set the default CLI profile
aws configure set default.sso_start_url "$SSO_START_URL"
aws configure set default.sso_region "$SSO_REGION"
aws configure set default.sso_account_id "$SSO_ACCOUNT_ID"
aws configure set default.sso_role_name "$SSO_ROLE_NAME"

# Set default region
aws configure set default.region "$CLI_DEFAULT_REGION"

# Set default output format
aws configure set default.output "$CLI_DEFAULT_OUTPUT_FORMAT"

# Run AWS EKS command to update kubeconfig
if [[ $profile_choice -eq 2 ]]; then
  aws eks --region "$CLI_DEFAULT_REGION" update-kubeconfig --name dev-cluster --role-arn "$ROLE_ARN"
fi
```

</details>

Usage:

```bash
make aws-config-set
```

## References

- [Kubernetes Official Kubectl Cheatsheet](https://kubernetes.io/docs/reference/kubectl/quick-reference/)
- [GitHub - Aytitech K8sFundamentals](https://github.com/aytitech/k8sfundamentals/blob/main/kubectl/README.md)