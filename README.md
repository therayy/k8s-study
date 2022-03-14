#### Q1. Task: Given an existing Kubernetes cluster running version 1.18.8, upgrade all of the Kubernetes control plane and node components on the master node only to version 1.19.0. You are also expected to upgrade `kubelet` and `kubectl` on the master node.
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

#### Q2. Task: From the pod label `name=cpu-user`, find pods running high CPU workloads and write the name of the pod consuiming most CPU to the file /opt/KUTROO401/KUTROO401.txt (which already exists).
```bash
kubectl config use-context k8s
``` 
<details>
  <summary>Answer</summary>

```bash
kubectl top -l name=cpu-utilizer –A echo 'pod name' >>/opt/KUT00401/KUT00401.txt

kubectl top pod --sort-by='cpu' --no-headers | head -1

or

kubectl top pods -l name=name-cpu-loader --sort-by=cpu
echo ‘top pod name' >>/opt/KUTR00401/KUTR00401.txt

or

kubectl top node --sort-by='cpu' --no-headers | head -1
kubectl top pod --sort-by='memory' --no-headers | head -1
kubectl top pod --sort-by='cpu' --no-headers | tail -1
```
</details>

#### Q3. Task: Check to see how many nodes are ready (not including nodes tainted NoSchedule) and wrtie the number to /opt/KUSC00402/kusc00402.txt
```bash
kubectl config use-context k8s
``` 
<details>
  <summary>Answer</summary>

```bash
kubectl get nodes
kubectl get node | grep -i ready |wc -l
kubectl describe nodes | grep ready | wc -l
kubectl describe nodes | grep -i taint | grep -i noschedule | wc -l
echo 3 > /opt/KUSC00402/kusc00402.txt

JSONPATH='{range .items[*]}{@.metadata.name}:{range @.status.conditions[*]}{@.type}={@.status};{end}{end}' \
 && kubectl get nodes -o jsonpath="$JSONPATH" | grep "Ready=True" > /opt/KUSC00402/kusc00402.txt
```
</details>