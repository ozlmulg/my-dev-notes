# Kubeadm

You can find the kubernetes
documentation [here.](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

## 1. Create virtual machines

```
$ multipass launch --name master -c 2 -m 2G -d 10G
$ multipass launch --name node1 -c 2 -m 2G -d 10G
```

* Connect to the master node and run `sudo hostnamectl set-hostname master`.  
  Connect to node1 and run `sudo hostnamectl set-hostname node1`.

## 2. Activate kernel modules and disable swap

```
$ sudo modprobe overlay
$ sudo modprobe br_netfilter
```

```
$ cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF
```

```
$ cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF
```

```
$ sudo sysctl --system
```

```
$ sudo swapoff -a
$ free -m
$ sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```

## 3. Install containerd

```
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
$ sudo apt update
$ sudo apt install containerd.io
$ sudo systemctl daemon-reload
$ sudo systemctl enable --now containerd
$ sudo systemctl start containerd
$ sudo mkdir -p /etc/containerd
$ sudo su -
$ containerd config default | tee /etc/containerd/config.toml
$ exit
$ sudo sed -i 's/            SystemdCgroup = false/            SystemdCgroup = true/' /etc/containerd/config.toml
$ sudo systemctl restart containerd

```

## 4. Install kubeadm

```
$ sudo ufw allow 6443/tcp
$ sudo ufw allow 2379:2380/tcp
$ sudo ufw allow 10250/tcp
$ sudo ufw allow 10259/tcp
$ sudo ufw allow 10257/tcp
$ sudo apt-get update
$ sudo apt-get install -y apt-transport-https ca-certificates curl
$ sudo mkdir -p -m 755 /etc/apt/keyrings
### If you want to install a Kubernetes version other than 1.31, update the v1.31 parts in the two commands below.
$ curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
$ echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
$ sudo apt-get update
$ sudo apt-get install -y kubelet kubeadm kubectl
$ sudo apt-mark hold kubelet kubeadm kubectl
```

## 5. Kubernetes cluster setup

```
$ sudo kubeadm config images pull

$ sudo kubeadm init --pod-network-cidr=192.168.0.0/16 --apiserver-advertise-address=<ip> --control-plane-endpoint=<ip>
```

```
$ mkdir -p $HOME/.kube
$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
$ sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

```
$ kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.1/manifests/tigera-operator.yaml
$ kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.1/manifests/custom-resources.yaml
```

```
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
kubectl taint nodes --all node-role.kubernetes.io/master-
```

## References

For more details, check the [website](https://github.com/aytitech/k8sfundamentals/blob/main/setup/README.md).