# k8s on Clear
Kubernetes on a Clear Linux VM.

## Getting Started
This project uses [AntonioMeireles/ClearLinux-packer Vagrant Box](https://github.com/AntonioMeireles/ClearLinux-packer).

(\* Make sure you have the plugin installed!!! (see the link above.))

```bash
# cd into this directory
vagrant up
vagrant ssh

# now, inside the VM
# Flannel pod networking
sudo kubeadm init --pod-network-cidr 10.244.0.0/16 --ignore-preflight-errors=SystemVerification
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/bc79dd1505b0c8681ece4de4c0d86c5cd2643275/Documentation/kube-flannel.yml
```

## Credits
@AntonioMeirales for his awesome Clear Linux box!! (I am a fan.)