### Q1. Task: Given an existing Kubernetes cluster running version 1.18.8, upgrade all of the Kubernetes control plane and node components on the master node only to version 1.19.0. You are also expected to upgrade `kubelet` and `kubectl` on the master node.
```bash
kubectl config use-context mk8s
``` 
> Note: 
> Be sure to drain the master node before upgrading it and uncordon it after the upgrade. Do not upgrade the worker nodes, etcd, the container manager, the CNI plugin, the DNS service or any other addons.
<details>
  <summary>Answer</summary>

### [A1](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/)
```bash
kubectl drain <node-to-drain> --ignore-daemonsets
root@controlplane:~# apt update

apt-get update && \
apt-get install -y --allow-change-held-packages kubeadm=1.22.x-00

kubeadm version
kubeadm upgrade plan
kubeadm upgrade apply v1.20.0
apt-get update && \
    apt-get install -y --allow-change-held-packages kubelet=1.22.x-00 kubectl=1.22.x-00

sudo systemctl daemon-reload
sudo systemctl restart kubelet
kubectl uncordon <node-to-drain>
```
</details>