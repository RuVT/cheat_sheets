# Preparation
## Disable the swap
```
# disable the swap, required by kubelet
sudo swapoff -a

# commet the line related to swap in here
vim /etc/fstab
```

## Enable ipv4 forwarding
```
# Set config to 1, requirement by kubeadm
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward = 1
EOF

# Apply the change
sudo sysctl --system
```

## Disable appmonitor
```
# This service monitor and block any call to an app that does not have a rule
sudo systemctl stop apparmor
sudo systemctl disable apparmor
```

## Install packages

```
# Update repo list
sudo apt update

# install basic tools
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

# Create directory `/etc/apt/keyrings` if does not exist
sudo mkdir -p -m 755 /etc/apt/keyrings

# Add kubernetes keyring to gpg and add the repo to the source list
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.33/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.33/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

# Update repo list again
sudo apt update

# Install packages
sudo apt-get install -y kubelet kubeadm kubectl containerd

# Set containerd cgroup config as systemd
sudo mkdir /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
sudo vim /etc/containerd/config.toml
# update this config
# [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
#            ...
#            SystemdCgroup = true
#


# Start container runntime
sudo systemctl enable containerd
sudo systemctl start containerd
```

## Init cluster control-plane wiht kubeadm
```
# copy the kubeadm join command from the next output
# kubeadm init
echo "
# kubeadm-config.yaml
kind: ClusterConfiguration
apiVersion: kubeadm.k8s.io/v1beta4
kubernetesVersion: v1.21.0
---
kind: KubeletConfiguration
apiVersion: kubelet.config.k8s.io/v1beta1
cgroupDriver: systemd
" > /tmp/kubeadm-config.yaml
sudo kubeadm init --apiserver-advertise-address=<IP> --pod-network-cidr=10.244.0.0/16

# add the kubectl config to the ~/.kube
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## Add CNI Network Pluging
```
sudo modprobe bridge
sudo modprobe br_netfilter

kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml

```


## Join workers with kubeadm
```
sudo kubeadm join <IP>:6443 --token zr...6 --discovery-token-ca-cert-hash sha256:df8...4
```

