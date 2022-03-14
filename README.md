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

#### Q4. Task: Monitor the logs of pod `foobar` and 
- Extract log lines corresponding to error `unable-to-access-website`
- Write them to `/opt/KUTR00101/foobar`
```bash
kubectl config use-context k8s
``` 
<details>
  <summary>Answer</summary>

```bash
kubectl logs foobar  | grep 'unable-to-access-website' > /opt/KULM00201/foobar
```
</details>

#### Q5. Task: Schedule a pod as follows:
- Name: `nginx-kusc00401`
- Image: `nginx`
- Node selector: `disk=ssd`
```bash
kubectl config use-context k8s
``` 
<details>
  <summary>Answer</summary>

### [Answer 5](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/)

```bash
vi pod.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-kusc00101
  
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  nodeSelector:
    disktype: ssd
```
```bash
kubectl create –f pod.yaml \
kubectl get pods
```
</details>

#### Q6. Task: List all persistent volumes sorted by capacity, saving the full kubectl output to `/opt/KUCC00102/volume_list`. Use kubectl 's own functionality for sorting the output, and do not manipulate it any further.

<details>
  <summary>Answer</summary>

### [Answer 6](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
```bash
# List PersistentVolumes sorted by capacity
kubectl get pv --sort-by=.spec.capacity.storage
```
```bash
kubectl get pv  --sort-by = .spec.capacity.storage > /opt/ KUCC00102/volume_list
```
</details>

#### Q7. Task: Create a file: `/opt/KUCC00302/kucc00302.txt` that lists all pods that implement service `baz` in namespace `development`. The format of the file should be one pod name per line.
> Note: selector: name=foo
<details>
  <summary>Answer</summary>

### [Answer 7](https://kubernetes.io/docs/tasks/configure-pod-container/configure-volume-storage/)
```bash
kubectl describe service baz –n development
```
```bash
kubectl get pods –l name=foo –n development –o NAME > /opt/KUCC00302/kucc00302.txt
```
</details>

#### Q8. Task: Create a pod as follows:
- Name: `non-persistent-redis`
- container Image: `redis`
- Volume with name: `cache-control`
- Mount path: `/data/redis`
- The pod should launch in the staging namespace be persistent.

<details>
  <summary>Answer</summary>

### [Answer 8](https://kubernetes.io/docs/tasks/configure-pod-container/configure-volume-storage/)
```bash
vi volume.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: non-persistent-redis
  namespace: staging
spec:
  containers:
  - name: redis
    image: redis
    volumeMounts:
    - name: cache-control
      mountPath: /data/redis
  volumes:
  - name: cache-control
    emptyDir: {}
```
```bash
kubectl create –f volume.yaml
```
```bash
kubectl get pods -n staging
```
</details>

#### Q9. Task: List `nginx-dev` and `nginx-prod` pod and delete those pods

<details>
  <summary>Answer</summary>

```bash
# List PersistentVolumes sorted by capacity
kubectl get pods -o wide
```
```bash
kubectl delete pods nginx-dev nginx-prod
```
</details>

#### Q10. Task: A Kubernetes worker node, named `wk8s-node-0` is in state `NotReady`, investigate why this is the case, and perform any appropriate steps to bring the node to a `Ready` state, ensuring that any changes are made permanent.

```bash
kubectl config use-context wk8s
``` 
> You can `ssh` to the failed node using:
>> 
```bash
ssh wk8s-node-0   
```
> You can assume elevated privileges on the node with the following command:
>>
```bash
sudo -i
```

<details>
  <summary>Answer</summary>

```bash
kubectl get nodes  
kubectl describe node wk8s-node-0 #view faulty node info
ssh wk8s-node-0 #enter node01
ps –aux | grep kubelet
sudo -i
systemctl status kubelet
systemctl start kubelet
systemctl enable kubelet
```
</details>

#### Q11. Task: Given a partially-functioning Kubernetes cluster, identify symptoms of failure on the cluster. Determine the node, the failing service, and take actions to bring up the failed service and restore the health of the cluster. Ensure that any changes are made permanently.

> You can `ssh` to the relevant I nodes:
>> 
```bash
[student@node-1]$ ssh <nodename>
```
> You can assume elevated privileges on the node with the following command:
>>
```bash
[student@nodename]$ sudo –i
```

<details>
  <summary>Answer</summary>

```bash
cat /var/lib/kubelet/config.yaml
```
```bash
staticpodpath: /etc/kubernates/manifests
systemctl restart kublet
systemctl enable kublet
```
</details>

#### Q12. Task: Set the node named `ek8s-node-1` as unavailable and reschedule all the pods running on it.

```bash
[student@node-1] > ssh ek8s
```
```bash
kubectl config use-context ek8s
```

<details>
  <summary>Answer</summary>
```bash
kubectl cordon ek8s-node-1
kubectl drain ek8s-node-1 --delete-local-data --ignore-daemonsets --force
```
</details>

#### Q13. Task: Configure the `kubelet systemd-managed service`, on the node labelled with `name=wk8s-node-1`, to launch a pod containing a single container of image `httpd` named `webtool` automatically. Any `spec` files required should be placed in the `/etc/kubernetes/manifests` directory on the node.

> You can `ssh` to the appropriate node using:
>> 
```bash
[student@node-1]$ ssh wk8s-node-1
```
> You can assume elevated privileges on the node with the following command:
>>
```bash
[student@wk8s-node-1]$ sudo –i
```
<details>
  <summary>Answer</summary>
```bash
kubectl cordon ek8s-node-1
kubectl drain ek8s-node-1 --delete-local-data --ignore-daemonsets --force
```
</details>