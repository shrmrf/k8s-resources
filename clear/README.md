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
sudo kubeadm init --pod-network-cidr 10.244.0.0/16 --ignore-preflight-errors=SystemVerification
```

## Problem
I was getting this error...
```
...
[kubelet-check] The HTTP call equal to 'curl -sSL http://localhost:10248/healthz' failed with error: Get http://localhost:10248/healthz: dial tcp [::1]:10248: connect: connection refused.
... 
		Unfortunately, an error has occurred:
			timed out waiting for the condition
```

## Solution
I came up with the solution while taking a look at the `kubelet.service` file
```ini
[Unit]
Description=kubelet: The Kubernetes Node Agent
Documentation=http://kubernetes.io/docs/

[Service]
Environment="KUBELET_KUBECONFIG_ARGS= --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf"
Environment="KUBELET_SYSTEM_PODS_ARGS=--pod-manifest-path=/etc/kubernetes/manifests --allow-privileged=true"
Environment="KUBELET_NETWORK_ARGS=--network-plugin=cni --cni-conf-dir=/etc/cni/net.d --cni-bin-dir=/usr/libexec/cni"
Environment="KUBELET_DNS_ARGS=--cluster-dns=10.96.0.10 --cluster-domain=cluster.local"
Environment="KUBELET_AUTHZ_ARGS=--authorization-mode=Webhook --client-ca-file=/etc/kubernetes/pki/ca.crt"
Environment="KUBELET_CADVISOR_ARGS=--cadvisor-port=0"
Environment="KUBELET_EXTRA_ARGS=--container-runtime=remote --container-runtime-endpoint=unix:///var/run/crio/crio.sock --runtime-request-timeout=30m"
ExecStart=
ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_SYSTEM_PODS_ARGS $KUBELET_NETWORK_ARGS $KUBELET_DNS_ARGS $KUBELET_AUTHZ_ARGS $KUBELET_CADVISOR_ARGS $KUBELET_EXTRA_ARGS
Restart=on-failure
StartLimitInterval=300
StartLimitBurst=6
RestartSec=10

[Install]
WantedBy=multi-user.target
```

`KUBELET_EXTRA_ARGS` is looking for `crio.sock` 

I need to do some more exploration on how I can make it more robust/fix the (really, unneeded) dependency on `crio.service`...

## Credits
@AntonioMeirales for his awesome Clear Linux box!! (I am a fan.)