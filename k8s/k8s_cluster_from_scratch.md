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
net.ipv4.ip_forward = 1
EOF

# Apply the change
sudo sysctl --system
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

# Start container runntime
sudo systemctl enable containerd
sudo systemctl start containerd
```

## Init cluster control-plane wiht kubeadm
```
# copy the kubeadm join command from the next output
kubeadm init
```


